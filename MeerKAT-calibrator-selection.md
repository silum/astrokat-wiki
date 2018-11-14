# Aim
User tool to find the closest, good calibrators to a target, as well as create a CSV catalogue file.   
`python astrokat-cals.py -h`

#### Basic functionality
The tool requires that a target, or a catalogue file be specified.   
A single target is specified with the `--target` argument,    
while catalogue file containing multiple targets are provided with the `--infile` argument.   
* Format for specifying a single target is simply `--target <name> <ra> <dec>`   
* Providing multiple targets in an input file simply follows the typical target specification definition of the observation [CSV catalogue](https://github.com/ska-sa/astrokat/wiki/Observation-catalogues) files.
Default is to select the first target in the list of targets to find the indicated calibrators for. This is not always desirable and to indicate which targets in the file to use when selecting calibrators the `calref` tag must be added to that target's observation information.   
For example the input file: `cat sample_targetlist_for_cals.csv`
```
# Catalogue needed for the AR1 mosaic tests
# AR1 mosaic NGC641
NGC641_02D02, radec target, 01:39:25.009, -42:14:49.216
NGC641_03D02, radec target, 01:37:01.491, -42:14:49.216
NGC641_02D03, radec target, 01:40:36.768, -42:37:41.000
NGC641_03D03, radec target calref, 01:38:13.250, -42:37:41.000
NGC641_04D03, radec target, 01:35:49.732, -42:37:41.000
NGC641_02D04, radec target, 01:39:25.009, -43:00:32.784
NGC641_03D04, radec target, 01:37:01.491, -43:00:32.784
```

* In addition to the targets, the user must also specify the type of calibrators to select using the `--cal-tags` argument. Current available options are `gain, bp, flux, pol`

| Tag | Calibrator Type |
| --- | --- |
| gain | gain |
| bp | bandpass |
| flux | flux |
| pol | polarisation |

#### Additional information
* Calibrators are selected using observatory calibrator catalogues located in `katconfig/user/catalogues`.   
To use a user specified catalogue the `--cat-path` argument must be specified giving the full path to the folder where the desired catalogues are located.   
**Important**: The reader should note that the current implementation of `astrokat-cals.py` assume a specific calibrator naming convension: `Lband-<calibrator_calssification>-calibrators.csv`.   
The implementation will be updated as new requirements are identified based on usage.

* Optional input arguments such as `--pi`, `--contact` and `--prop-id`, provides metadata that is added when the observation catalogue file is created.
The purpose of this information is simply informational and is supplementary to the target specification. 


# Detailed examples

## Creating a catalogue

### Using a single target as input
Most basic implementation is to view available calibrators for a target   
`python astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain bp flux`

Resulting in display output:
```
Observation Table for 2018-10-20 12:45:51.596
Sources         Class           Rise Time       Set Time        Separation      Notes
Observation Table for 2018/11/14 08:43:24
Times in UTC when target is above the default horizon = 20 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     14:37:30        02:37:52        115.08          separation from Sun
J0010-4153      radec bpcal                     0:10:52.52      -41:53:10.8     13:12:18        01:09:07        16.13 ***       separation from NGC641_03D03
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     14:59:06        02:50:56        3.72
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     15:46:16        06:27:16        31.01 ***
```
With the `***` a visual aid to the user to draw attention that the closest calibrator found was more than 15 degrees away from the target.

If the Python `matplotlib` library is available, an elevation graph will also be displayed.
![astrokat-cals-single-target](https://github.com/ska-sa/astrokat/blob/master/wiki/astrokat-cals-single-target.png)   

Useful additional options:
* To create a catalogue file a filename for the output catalogue has to be provided by adding the `--outfile` argument.   
`python astrokat-cals.py --prop-id 'SCI-dateinitials-nr' --pi 'No One' --contact 'dummy@ska.ac.za' --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain bp flux --outfile ../output/test_NGC641_03D03.csv`
`Observation catalogue ../output/test_NGC641_03D03.csv`   
The `--outfile` argument is **very important when selecting flux and polarisation calibrators**. These calibrators comes with flux model coefficients that must be added to the targets and is only done in the output file.
```
# Observation catalogue for proposal ID SCI-dateinitials-nr
# PI: No One
# Contact details: dummy@ska.ac.za
J0010-4153 | 0008-421, radec bpcal, 0:10:52.52, -41:53:10.8, (145.0 20000.0 -16.93 15.39 -4.21 0.3496)
J0155-4048 | 0153-410, radec gaincal, 1:55:37.06, -40:48:42.4
J0408-6545 | 0408-658, radec bpcal fluxcal, 4:08:20.38, -65:45:09.6, (145.0 18000.0 -0.979 3.366 -1.122 0.0861)
NGC641_03D03, radec target, 1:38:13.25, -42:37:41.0
```


### For multiple targets in an input file
Using the example input file above listing multiple galactic pointings   
`python astrokat-cals.py --prop-id 'SDP-calselect-test' --cal-tags gain bp pol flux delay --outfile ../output/SDP-calselect-test_catalogue.csv --infile ../input/sample_targetlist_for_cals.csv --datetime '2018-08-06 12:34'`

Producing screen output:   
```
Observation catalogue ../output/SDP-calselect-test_catalogue.csv

Observation Table for 2018-08-06 12:34:00
Sources         Class           Rise Time       Set Time        Separation      Notes
T3R04C06        target          08:07:52        19:48:16        127.09          separation from Sun
T4R00C02        target          07:57:33        19:36:28        124.96
T4R00C04        target          07:52:47        19:35:17        124.30
T4R00C06        target          07:47:57        19:34:02        123.63
T4R01C01        target          08:01:17        19:39:42        125.66
T4R01C03        target          07:56:33        19:38:34        124.99
T4R01C05        target          07:51:45        19:37:23        124.32
T4R02C02        target          08:00:19        19:41:50        125.69
T4R02C04        target          07:55:33        19:40:42        125.02
J1331+3030      polcal          07:43:46        12:30:09        85.95 ***       separation from T4R01C01
J1744-5144      gaincal         07:55:44        20:44:37        14.92           separation from T4R01C01
J1939-6342      bpcal,fluxcal   09:05:36        23:24:36        33.72 ***       separation from T4R01C01
```
Creates the catalogue file:   
```
#7th set of pointings of the Galactic plane Mosaic
# Observation catalogue for proposal ID SDP-calselect-test
# PI: None
# Contact details: None
J1331+3030 | 3C286, radec polcal, 13:31:08.29, 30:30:33.0, (50.0 50000.0 0.0181 1.592 -0.5011 0.0357)
J1744-5144 | 1740-517, radec gaincal, 17:44:25.47, -51:44:43.1, (145.0 99000.0 -3.694 2.745 -0.3655 -0.015)
J1939-6342 | 1934-638, radec bpcal fluxcal, 19:39:25.05, -63:42:43.6, (408.0 8640.0 -30.77 26.49 -7.098 0.6053)
T3R04C06, radec target, 17:22:27.47, -38:12:09.4
T4R00C02, radec target, 17:11:22.47, -37:51:51.1
T4R00C04, radec target, 17:08:23.04, -38:39:29.8
T4R00C06, radec target, 17:05:19.54, -39:26:50.5
T4R01C01, radec target, 17:14:51.98, -37:45:16.2
T4R01C03, radec target, 17:11:55.13, -38:33:15.5
T4R01C05, radec target, 17:08:54.28, -39:20:57.4
T4R02C02, radec target, 17:15:26.59, -38:26:37.0
T4R02C04, radec target, 17:12:28.40, -39:14:39.4
```
Elevation plot:
![astrokat-cals-multiple-target](https://github.com/ska-sa/astrokat/blob/master/wiki/astrokat-cals-multiple-target.png)

If the `--datetime` argument is not specified, the current time will be used for all time conversions and elevation calculations

The impact of the `--datetime` argument can be shown in the following example, using the 55 minimum solar separation angle threshold, `--solar-angle`, to highlight the differences:

`python astrokat-cals.py --prop-id 'SDP-calselect-test' --cal-tags gain bp pol flux delay --infile ../test/sample_targetlist_for_cals.csv --text-only --solar-angle=55`
```
Observation Table for 2018-10-20 14:29:47.782
Sources         Class           Rise Time       Set Time        Separation      Notes
T3R04C06        target          08:07:52        19:48:16        56.82           separation from Sun
T4R00C02        target          07:57:33        19:36:28        54.62 ***
T4R00C04        target          07:52:47        19:35:17        54.29 ***
T4R00C06        target          07:47:57        19:34:02        53.96 ***
T4R01C01        target          08:01:17        19:39:42        55.26
T4R01C03        target          07:56:33        19:38:34        54.92 ***
T4R01C05        target          07:51:45        19:37:23        54.59 ***
T4R02C02        target          08:00:19        19:41:50        55.55
T4R02C04        target          07:55:33        19:40:42        55.22
J1331+3030      polcal          07:43:46        12:30:09        85.95 ***       separation from T4R01C01
J1744-5144      gaincal         07:55:44        20:44:37        14.92           separation from T4R01C01
J1939-6342      bpcal,fluxcal   09:05:36        23:24:36        33.72 ***       separation from T4R01C01
```

Compared to:
`python astrokat-cals.py --prop-id 'SDP-calselect-test' --cal-tags gain bp pol flux delay --infile ../test/sample_targetlist_for_cals.csv --text-only --solar-angle=55 --datetime '2018-08-06 12:34'`
```
Observation Table for 2018-08-06 12:34:00
Sources         Class           Rise Time       Set Time        Separation      Notes
T3R04C06        target          08:07:52        19:48:16        127.09          separation from Sun
T4R00C02        target          07:57:33        19:36:28        124.96
T4R00C04        target          07:52:47        19:35:17        124.30
T4R00C06        target          07:47:57        19:34:02        123.63
T4R01C01        target          08:01:17        19:39:42        125.66
T4R01C03        target          07:56:33        19:38:34        124.99
T4R01C05        target          07:51:45        19:37:23        124.32
T4R02C02        target          08:00:19        19:41:50        125.69
T4R02C04        target          07:55:33        19:40:42        125.02
J1331+3030      polcal          07:43:46        12:30:09        85.95 ***       separation from T4R01C01
J1744-5144      gaincal         07:55:44        20:44:37        14.92           separation from T4R01C01
J1939-6342      bpcal,fluxcal   09:05:36        23:24:36        33.72 ***       separation from T4R01C01
```

Similarly, a PDF containing the elevation graph and target information will be generated by adding the `--report` argument:   
`python astrokat-cals.py --prop-id 'SDP-calselect-test' --cal-tags gain bp pol flux delay --outfile ../output/SDP-calselect-test_catalogue.csv --infile ../test/sample_targetlist_for_cals.csv --solar-angle=55 --datetime '2018-08-06 12:34' --report`   
[../output/SDP-calselect-test_catalogue.pdf](https://github.com/ska-sa/astrokat/blob/master/wiki/SDP-calselect-test_catalogue.pdf)


## Viewing a catalogue
An updated elevation plot of catalogue targets can be shown by adding the `--view` option   
* `python astrokat-cals.py --view ../output/test_SCI-20180624FC-01_Abell13.csv`   
* `python astrokat-cals.py --view ../output/SDP-calselect-test_catalogue.csv --text-only --solar-angle=55`
* `python astrokat-cals.py --view ../output/SDP-calselect-test_catalogue.csv --text-only --solar-angle=56 --datetime '2018-08-06 12:34'`


# Important notes to user

* The default folder for the standard calibrator catalogues are `katconfig/user/catalogues/`.
This folder requires the katconfig repository which is not always available.   
```
Unhandled exception <type 'exceptions.RuntimeError'> : Could not access calibrator catalogue default location
add explicit location of catalogue folder using --cat-path <dirname>
```   
The standard catalogues will be made available in a separate repository for local use, with the updated command   
`python astrokat-cals.py --prop-id 'SCI-20180624FC-01' --pi 'Fernando Camilo' --contact 'fernando@ska.ac.za, sharmila@ska.ac.za' --target 'Abell 13' '00:13:32.2' '-19:30:03.6' --cal-tags gain flux --outfile ../output/test_SCI-20180624FC-01_Abell13.csv  --cat-path ../calibrators/`   

* The generation graphical output such as the elevation plot and PDF report creation is not always available on the observation systems. If the python `matplotlib` package is not available, the script will default to the text output displayed to screen and indicate this to the user with the output:   
```
Required matplotlib functionalities not available
Cannot create elevation plot or generate report
Only producing catalogue file and output to screen
```

