# Building an observation plan

## Creating an observation file
1. Starting off with only a target and coordinates -- create an observation CSV catalogue
```
astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain flux --cat-path astrokat/catalogues/ --outfile 'astrokat_catalogue.csv' --lst --datetime '2019-2-6 14:52:48' --horizon 20
```
2. List rise and set times in LST and update listed targets accordingly
```
astrokat-cals.py --view astrokat_catalogue.csv --lst --horizon 20
```
3. Convert CSV catalogue to YAML observation file
```
astrokat-catalogue2obsfile.py --infile astrokat_catalogue.csv --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --outfile astrokat_obsfile.yaml
```
4. Update the .yaml file to correct LST range, observation period or source observation sequences, as needed.
```
astrokat-cals.py --view astrokat_obsfile.yaml --lst --text-only --horizon 20
```


## Adding or removing observation targets
**It is strongly advised not to update an existing YAML file manually.**

Add or remove targets and calibrators from an existing observation should only be done by editing the observation CSV catalogue.   
If this catalogue does not exist, regenerate a CSV catalogue using the steps described above.

For an existing CSV catalogue, the following steps can be used to obtain an update observation YAML file.

1. Find calibrators for new target, but this time do not create a CSV file, only display the output to screen
```
astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain flux --cat-path astrokat/catalogues/ --lst --horizon 20
```

2. Add target and calibrator information to the existing observation CSV catalogue

3. Convert the updated CSV catalogue to YAML observation file
```
astrokat-catalogue2obsfile.py --infile astrokat_catalogue.csv --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --outfile astrokat_obsfile.yaml
```

4. Display and verify update information   
When new targets are added, or redundant targets are removed, it is most important to verify that the LST range specified are still relevant since this will be used to evaluate the viability of the observation.
```
astrokat-cals.py --view astrokat_obsfile.yaml --lst --horizon 20
```

## When to schedule an observation file
Rise and set times for all sources in an observation file is related to the LST range over which the sources are visible.

An observation using the YAML framework can be scheduled anywhere within this range.

For observation schedule creation the visibility in UTC must be displayed and evaluated.
It can also be displayed starting from the desired UTC schedule time using the `--datetime` option.
Then validate LST values at the bottom are within range and the sources rise and set starting at the UCT time specified.
```
astrokat-cals.py --view astrokat_obsfile.yaml --horizon 20
astrokat-cals.py --view astrokat_obsfile.yaml --horizon 20 --datetime '2019-2-6 14:52:48'
```


***
***


## Detail overview
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






### X. [Optional] Verify observation file
Typical things to check, is the cadence sources visited often enough and close enough to the interval specified.
The sequence between targets and gain calibrators are as expected.
How much time is spent on each source throughout the observation.

