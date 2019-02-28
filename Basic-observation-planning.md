# Building an observation file
Fundamentally all MeerKAT observation are represented by 2 modules:
* The [observation file](https://github.com/ska-sa/astrokat/wiki/Basic-observation-planning/_edit#creating-the-observation-file) that describes the observation on a per target basis, and
* the [observation script](https://github.com/ska-sa/astrokat/wiki/Basic-observation-planning/_edit#optional-verify-and-refine-the-observation) that interprets and execute the observation file when scheduled.

The first represents the science requirements of the observation and may include some telescope related options as well.   
While the second represents the telescope executing the observation and forms the basis for guaranteeing consistency between planning and observation.


## Creating the observation file
### 1. Create an observation catalogue
For an identified observation target
* select standard MeerKAT calibrators if not provided
* list rise and set time in LST for all observation sources
* create a CSV catalogue listing sources and J2000 coordinates

**Note**: Current target coordinate input RA DEC only with string format coordinates

Simple usage example
```
astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain flux --cat-path astrokat/catalogues/ --outfile 'astrokat_catalogue.csv' --lst --datetime '2019-2-6 14:52:48' --horizon 20
```
Refer to [MeerKAT calibrator selection](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrator-selection) for all options

The output of this script will tabulate the target and selected calibrators to screen.
If the `matplotlib` package is installed the script will also display an elevation plot for all sources.
Use the table output and plot to decide if you want to discard any calibrators, update any calibrator selection or need to manually add a calibrator if the MeerKAT standard calibrators are not suitable.
```
Observation catalogue astrokat_catalogue.csv

Observation Table for 2019/2/6 14:52:48 (UTC)
Times listed in LST for target rise and set times
Target visible when above 20.0 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     19:37:49.93     7:40:11.37      61.06           separation from Sun
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     19:59:29.85     7:53:17.65      3.72            separation from NGC641_03D03
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     20:46:45.33     11:30:13.91     31.01 ***
J1939-6342      radec bpcal fluxcal             19:39:25.05     -63:42:43.6     12:30:28.09     2:51:43.91      52.49 ***
```

In the example output above, both `J0408-6545` and `J1939-6342` was added to the selection to ensure best coverage of the target LST range. But `J1939-6342` is not really needed since `J0408-6545` has LST rise and set times close to those of the target, thus `J1939-6342` can be manually removed from the output CSV file it is not needed.


### 2. List rise and set times in LST
Once a catalogue has been created the rise and set times of all the sources in the catalogue can be listed.
Along with the elevation output plot, this can be used to identify sources that may not be needed (as mentioned above), or to show gaps in the LST coverage and the need to manually add additional sources.

In this example the auto-suggested flux calibrator `J1939-6342` is not needed and can be removed.   
(The CSV catalogue file is a standard text file that can be changed manually by your favourite editor.)

Resulting in the desired catalogue, which can now be used to add information to a science proposal, or an observation plan.   
All coordinates are listed in the currently expected (RA,DEC) string format and MeerKAT selected calibrator are astrometric J2000 standard.
All sources list LST rise and set times.

```
astrokat-cals.py --view astrokat_catalogue.csv --lst --horizon 20

Observation Table for 2019/2/7 06:37:44 (UTC)
Times listed in LST for target rise and set times
Target visible when above 20.0 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     19:37:49.93     7:40:11.37      60.71           separation from Sun
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     20:46:45.33     11:30:13.91     31.01 ***       separation from NGC641_03D03
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     19:59:29.85     7:53:17.65      3.72
```
![astrokat csv catalogue](https://github.com/ska-sa/astrokat/blob/master/wiki/astrokat_catalogue.png)


### 3. Convert CSV catalogue to YAML observation file
Setting up the observation file is required in order to run the observation, and also if the user wants to verify the observation pattern and timings are as desired.
```
astrokat-catalogue2obsfile.py --infile astrokat_catalogue.csv --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --outfile astrokat_obsfile.yaml
```
Full detail and options can be found on the [Catalogues to observation files](https://github.com/ska-sa/astrokat/wiki/Catalogues-to-observation-files) wiki page
```
# Observation catalogue for proposal ID None
# PI: None
# Contact details: None
durations:
  obs_duration: 35400.0
observation_loop:
  - LST: 19:37-11:30
    target_list:
      - name=J0408-6545 | 0408-658, radec=4:08:20.38 -65:45:09.6, tags=bpcal fluxcal, duration=180.0, cadence=1800.0, model=(145.0 18000.0 -0.979 3.366 -1.122 0.0861)
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=NGC641_03D03, radec=1:38:13.25 -42:37:41.0, tags=target, duration=300.0
```

The auto-generated LST range is the widest possible range of observation.
Viewing the LST rise and set times of the targets listed can be used to assist with validating or updating this range
`LST: 19:37-11:30`

```
astrokat-cals.py --view astrokat_obsfile.yaml --lst --text-only --horizon 20

Observation Table for 2019/2/7 08:34:04 (UTC)
Times listed in LST for target rise and set times
Target visible when above 20.0 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     19:37:49.93     7:40:11.37      60.66           separation from Sun
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     20:46:45.33     11:30:13.91     31.01 ***       separation from NGC641_03D03
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     19:59:29.85     7:53:17.65      3.72
```
From the output it is clear that the start LST time, `19:37`, is set by the target/gain calibrator pair, the end LST time, `11:30`, is set by the flux calibrator that is visible much longer than the target.
The LST range in the observation file will be updated as follows:
* If the bandpass calibrator must be observed first, the start time has to be adjusted to reflect the rise time of 
`J0408-6545`.
In this case there is a cadence and the calibrator can be observed as soon as it rises.
* The gain calibrator will be observed before the target, and thus the gain calibrator rise time could be used.
This is not strictly necessary since the observation is set to repeat the targets for the length of the observation duration, so the observation can start observing visible targets while waiting for other targets to rise.
* The set time in the LST range has to be corrected though, since we would like to restrict it only to be around the target/calibrator pair time range.
Thus the LST end time is set to `7:40`, the target set time.
```
# Observation catalogue for proposal ID None
# PI: None
# Contact details: None
durations:
  obs_duration: 35400.0
observation_loop:
  - LST: 19:37-7:40
    target_list:
      - name=J0408-6545 | 0408-658, radec=4:08:20.38 -65:45:09.6, tags=bpcal fluxcal, duration=180.0, cadence=1800.0, model=(145.0 18000.0 -0.979 3.366 -1.122 0.0861)
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=NGC641_03D03, radec=1:38:13.25 -42:37:41.0, tags=target, duration=300.0
```

**Note**: Targets as listed in the YAML file will be observed in sequence and repeated as necessary, except for cadence targets.
It is thus very important to remember to update the observation YAML file and insert source repetitions if required.

As an example, if the above observation was to observe all sources listed just once as a pilot observation, and with gain calibrator before and after the target to obtain good calibration, the observation file will be updated as follow:
* Since no `maximum-duration` will have been specified during conversion, the `durations` section in the observation file will not be present.
This indicates to the observation script that the list of targets are only to be traversed once.
* The bandpass calibrator will not be scheduled first, so it is important to update the LST start time to be the rise time of the bandpass calibrator
* Also, since the targets will be observed once, only a the LST start time is needed, and not a range as above.
```
# Observation catalogue for proposal ID None
# PI: None
# Contact details: None
observation_loop:
  - LST: 20:46
    target_list:
      - name=J0408-6545 | 0408-658, radec=4:08:20.38 -65:45:09.6, tags=bpcal fluxcal, duration=180.0, cadence=1800.0, model=(145.0 18000.0 -0.979 3.366 -1.122 0.0861)
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=NGC641_03D03, radec=1:38:13.25 -42:37:41.0, tags=target, duration=300.0
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
```


## [Optional] Verify and refine the observation

Detail descriptions of all the information that can be contained in a YAML file can be found on the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) wiki page.

Basic information for validating the observation sequence of a newly created observation file are as follows:

Central to verifying and refining the observation using newly generated YAML file is the `astrokat-observe.py` observation script.
The script can be used on the astronomers local computer to plan the setup and execution sequence of the observation, as well as verify that the observation target sequence and timing is as anticipated.
This will obviously be a simulated result, but the target sequence will be true and aim is to provide timing information close to actual time.

This is the same script that will be used by MeerKAT staff to verify and execute the observation, producing the commonly called dry-run and tasklog output.

Typical things to check when verifying the observation plan offline:
* Is the cadence sources visited often enough and close enough to the interval specified.
* The sequence between targets and gain calibrators are as expected.
* How much time is spent on each source throughout the observation.

The observation instruction is the same on all systems:
```
astrokat-observe.py --yaml astrokat_obsfile.yaml
```

Simply running the observation script by results in errors such as:
* All targets below horizon   
`No more targets to observe - stopping script instead of hanging around`

* No targets or only a single target were observed   
`Targets observed :`   
`J0408-6545 observed for 0.0 sec`   
`J0409-1757 observed for 0.0 sec`   
`UZ For observed for 0.0 sec`   

* Local LST does not match the LST range specified in the observation plan   
`Local LST outside LST range 19:37:00.00-7:40:00.00`

This is because the observation will always assume current time if it is not instructed to use a different time to simulate the observation.
Setting or adjusting the observation time to simulate the observation is done by adding the `start_time` option to the YAML observation file under the `durations` group.

```
# Observation catalogue for proposal ID None
# PI: None
# Contact details: None
durations:
  start_time: 2019-02-28 10:00:00
  obs_duration: 35400.0
observation_loop:
  - LST: 19:37-7:40
    target_list:
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=J0408-6545 | 0408-658, radec=4:08:20.38 -65:45:09.6, tags=bpcal fluxcal, duration=180.0, cadence=1800.0, model=(145.0 18000.0 -0.979 3.366 -1.122 0.0861)
      - name=NGC641_03D03, radec=1:38:13.25 -42:37:41.0, tags=target, duration=300.0
```

Running the observation script now will adopt the set observation start time and produce observation output similar to what the telescope will produce when executing the observation.


> astrokat-observe.py --yaml astrokat_obsfile.yaml
2019-02-28 07:52:51Z - Setting up telescope for observation
2019-02-28 07:52:51Z - Switch noise-diode off at 1551340377.0
2019-02-28 07:39:52Z - Found 3 target(s): 0 from 0 catalogue(s), 0 from default catalogue and 3 as target string(s)
2019-02-28 07:39:52Z - Imaging targets are ['NGC641_03D03']
2019-02-28 07:39:52Z - Bandpass calibrators are ['J0408-6545']
2019-02-28 07:39:52Z - Gain calibrators are ['J0155-4048']
2019-02-28 09:59:59Z - Slewing to first target
2019-02-28 10:00:44Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 10:04:00Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:05:21Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:10:22Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:11:29Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:16:31Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:17:38Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:22:40Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:23:47Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:28:49Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:29:56Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:34:57Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 10:38:13Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:39:33Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:44:35Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:45:42Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:50:44Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:51:51Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 10:56:53Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 10:58:00Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:03:01Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:04:08Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:09:10Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 11:12:26Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:13:46Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:18:48Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:19:55Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:24:57Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:26:04Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:31:06Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:32:12Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:37:14Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:38:21Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:43:23Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 11:46:38Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:47:59Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:53:01Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 11:54:08Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 11:59:10Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:00:16Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:05:18Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:06:25Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:11:27Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:12:34Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:17:36Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 12:20:51Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:22:12Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:27:14Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:28:21Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:33:22Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:34:29Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:39:31Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:40:38Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:45:40Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:46:47Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 12:51:49Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 12:55:04Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 12:56:25Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:01:26Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:02:33Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:07:35Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:08:42Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:13:44Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:14:51Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:19:53Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:21:00Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:26:01Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 13:29:17Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:30:37Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:35:39Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:36:46Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:41:48Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:42:55Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:47:57Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:49:04Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 13:54:05Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 13:55:12Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:00:14Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 14:03:30Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:04:50Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:09:52Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:10:59Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:16:01Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:17:08Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:22:10Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:23:16Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:28:18Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:29:25Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:34:27Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 14:37:42Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:39:03Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:44:05Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:45:12Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:50:14Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:51:20Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 14:56:22Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 14:57:29Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:02:31Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:03:38Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:08:40Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 15:11:55Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:13:16Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:18:18Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:19:25Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:24:26Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:25:33Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:30:35Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:31:42Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:36:44Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:37:51Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:42:53Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 15:46:08Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:47:29Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:52:30Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:53:37Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 15:58:39Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 15:59:46Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:04:48Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:05:55Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:10:57Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:12:04Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:17:05Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 16:20:21Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:21:41Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:26:43Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:27:50Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:32:52Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:33:59Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:39:01Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:40:08Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:45:09Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:46:16Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 16:51:18Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 16:54:34Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 16:55:54Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:00:56Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:02:03Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:07:05Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:08:12Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:13:14Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:14:20Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:19:22Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:20:29Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:25:31Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 17:28:46Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:30:07Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:35:09Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:36:16Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:41:18Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:42:24Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:47:26Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:48:33Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:53:35Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 17:54:42Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 17:59:44Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 18:02:59Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:04:20Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:09:22Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:10:29Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:15:30Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:16:37Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:21:39Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:22:46Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:27:48Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:28:55Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:33:57Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 18:37:12Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:38:33Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:43:35Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:44:41Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:49:43Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:50:50Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 18:55:52Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 18:56:59Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 19:02:01Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 19:03:08Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 19:08:09Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 19:11:25Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 19:12:45Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 19:17:47Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 19:18:54Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 19:23:56Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 19:25:03Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 19:30:05Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 19:31:12Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 19:36:13Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 19:37:20Z - Initialising Track target NGC641_03D03 for 300.0 sec
2019-02-28 19:42:22Z - Initialising Track bpcal J0408-6545 for 180.0 sec
2019-02-28 19:45:38Z - Initialising Track gaincal J0155-4048 for 65.0 sec
2019-02-28 19:46:58Z - Scheduled observation time lapsed - ending observation

2019-02-28 19:46:58Z - Observation loop statistics
2019-02-28 19:46:58Z - Desired observation time 35400.00 sec (590.00 min)
2019-02-28 19:46:58Z - Total observation time 35218.74 sec (586.98 min)
2019-02-28 19:46:58Z - Targets observed :
2019-02-28 19:46:58Z - J0155-4048 observed for 5590.0 sec
2019-02-28 19:46:58Z - J0408-6545 observed for 3240.0 sec
2019-02-28 19:46:58Z - NGC641_03D03 observed for 25500.0 sec

2019-02-28 19:46:58Z - Returning telescope to startup state
2019-02-28 19:46:58Z - Switch noise-diode off at 1551383224.0