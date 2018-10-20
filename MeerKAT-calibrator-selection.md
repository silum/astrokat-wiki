# Aim
User tool to find the closest, good calibrators to a target, as well as create a CSV catalogue file.   
`python astrokat-cals.py -h`

#### Basic functionality
The tool requires that a target, or a catalogue file be specified.   
A single target is specified with the `--target` argument,    
while catalogue file containing multiple targets are provided with the `--infile` argument.   
* Format for specifying a single target is simply `--target <name> <ra> <dec>`   
* Providing multiple targets in an input file simply follows the typical target specification definition of the observation CSV catalogue files [ADD LINK TO WIKI PAGE GIVING THIS DEFINITION HERE].
Default is to select the first target in the list of targets to find the indicated calibrators for. This is not always desirable and to indicate which targets in the file to use when selecting calibrators the `calref` tag must be added to that target's observation information.   
For example the input file: `cat sample_targetlist_for_cals.csv`
```
#7th set of pointings of the Galactic plane Mosaic
T3R04C06, radec, +17:22:27.46877, -38:12:09.4023
T4R00C02, radec, +17:11:22.47016, -37:51:51.0758
T4R00C04, radec, +17:08:23.04449, -38:39:29.8486
T4R00C06, radec, +17:05:19.53524, -39:26:50.4693
T4R01C01, radec calref, +17:14:51.97986, -37:45:16.2459
T4R01C03, radec, +17:11:55.13096, -38:33:15.4802
T4R01C05, radec, +17:08:54.27808, -39:20:57.3978
T4R02C02, radec, +17:15:26.58923, -38:26:36.9760
T4R02C04, radec, +17:12:28.40227, -39:14:39.4421
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
`python astrokat-cals.py --target 'Abell 13' '00:13:32.2' '-19:30:03.6' --cal-tags gain flux`

Resulting in display output:
```
Observation Table for 2018-10-20 12:45:51.596
Sources         Class           Rise Time       Set Time        Separation      Notes
Abell 13        target          15:41:37        02:01:43        143.42          separation from Sun
J0408-6545      bpcal,fluxcal   17:24:33        08:05:34        59.64 ***       separation from Abell 13
J2348-1631      gaincal         15:22:34        01:29:57        6.75            separation from Abell 13
```
With the `***` a visual aid to the user to draw attention that the closest calibrator found was more than 15 degrees away from the target.

If the Python `matplotlib` library is available, an elevation graph will also be displayed.   
ADD FIG HERE [astrokat-cals-single-target.png]

Useful additional options:
* To create a catalogue file a filename for the output catalogue has to be provided by adding the `--outfile` argument.
* To view the observation table output for a specific date, the `--datetime` argument can be added

### For multiple targets in an input file



## Viewing a catalogue


To quickly find calibrators for a target during planning
The `astrokat-cals.py` script will display the selected calibrators as well as relative separation angle information to screen + generate an elevation display for the target and suggested calibrators.
Current implementation will select and add the closest calibrator to the target and create a catalogue CSV file, with naming convension `<ProposalID>_<targetname>.csv`, from which the user can c&p the selected target information.
 
* When the closest calibrators are separate sources   
`python astrokat-cals.py  --prop-id 'SCI-20180624FC-01' --target 'Abell 13' '00:13:32.2' '-19:30:03.6' --cal-tags phase flux --output ../output/`
`Observation catalogue ../output/SCI-20180624FC-01_Abell13.csv`
```
> cat ../output/SCI-20180624FC-01_Abell13.csv
# Observation catalogue for proposal ID SCI-20180624FC-01
# PI: None
# Contact details: None
Abell 13, radec target, 0:13:32.20, -19:30:03.6
J2348-1631 | 2345-167, radec gaincal phasecal, 23:48:02.62, -16:31:12.6
J0025-2602 | 0023-263, radec fluxcal, 0:25:49.17, -26:02:12.8, (333.0 99000.0 -8.445 9.278 -2.78 0.2499)
```
```
Observation Table for 2018-10-16 08:38:53.168Z
Sources         Rise Time       Set Time        Separation Angle
Target
Abell 13        15:57:20Z       02:17:27Z       146.80 deg from Sun
Primary calibrators
J0025-2602      15:55:50Z       02:43:26Z       7.12 deg from Abell 13
Secondary calibrators
J2348-1631      15:38:17Z       01:45:41Z       6.75 deg from Abell 13
```

* If the closest calibrators are the same source   
`python astrokat-cals.py  --prop-id 'SCI-20180624FC-01' --target 'Abell 13' '00:13:32.2' '-19:30:03.6' --cal-tags gain flux --output ../output/`
`Observation catalogue ../output/SCI-20180624FC-01_Abell13.csv`
```
> cat ../output/SCI-20180624FC-01_Abell13.csv
# Observation catalogue for proposal ID SCI-20180624FC-01
# PI: None
# Contact details: None
Abell 13, radec target, 0:13:32.20, -19:30:03.6
J0025-2602 | 0023-263, radec gaincal fluxcal, 0:25:49.17, -26:02:12.8, (333.0 99000.0 -8.445 9.278 -2.78 0.2499)
```
```
Observation Table for 2018-10-16 09:30:46.504Z
Sources         Rise Time       Set Time        Separation Angle
Target
Abell 13        15:57:20Z       02:17:27Z       146.77 deg from Sun
Primary calibrators
J0025-2602      15:55:50Z       02:43:26Z       7.12 deg from Abell 13
Secondary calibrators
J0025-2602      15:55:50Z       02:43:26Z       7.12 deg from Abell 13
```

* To quickly construct an observation catalogue, as well as add some  observation related information   
`python astrokat-cals.py --prop-id 'SCI-20180624FC-01' --pi 'Fernando Camilo' --contact 'fernando@ska.ac.za, sharmila@ska.ac.za' --target 'Abell 13' '00:13:32.2' '-19:30:03.6' --cal-tags phase flux --report --output ../output/`   
Adding the `--report` argument will also generate a PDF file with the summary information shown above.   
`Observation catalogue ../output/SCI-20180624FC-01_Abell13.csv`
```
> cat ../output/SCI-20180624FC-01_Abell13.csv
# Observation catalogue for proposal ID SCI-20180624FC-01
# PI: Fernando Camilo
# Contact details: fernando@ska.ac.za, sharmila@ska.ac.za
Abell 13, radec target, 0:13:32.20, -19:30:03.6
J2348-1631 | 2345-167, radec gaincal, 23:48:02.62, -16:31:12.6
J0025-2602 | 0023-263, radec fluxcal, 0:25:49.17, -26:02:12.8, (333.0 99000.0 -8.445 9.278 -2.78 0.2499)
```
`Observation catalogue ./SCI-20180624FC-01_Abell13.pdf`    
![catalogue report](https://github.com/rubyvanrooyen/astrokat/blob/master/wiki/SCI-20180624FC-01_Abell13.png)

* For an existing catalogue the updated target observation times in UTC can be displays
```
> python astrokat-cals.py --view ../output/SCI-20180624FC-01_Abell13.csv --text-only

Observation Table for 2018-10-16 09:36:31.609Z
Sources         Rise Time       Set Time        Separation Angle
Target
Abell 13        15:57:20Z       02:17:27Z       146.77 deg from Sun
Primary calibrators
J0025-2602      15:55:50Z       02:43:26Z       7.12 deg from Abell 13
Secondary calibrators
J2348-1631      15:38:17Z       01:45:41Z       6.75 deg from Abell 13
```

* An updated UTC plot of catalogue targets can be shown by adding the `--view` option   
`python astrokat-cals.py --view ../output/SCI-20180624FC-01_Abell13.csv`   
![source elevation](https://github.com/rubyvanrooyen/astrokat/blob/master/wiki/elevation_utc_lst.png)

**Important notes to user**:

* The default folder for the standard calibrator catalogues are `katconfig/user/catalogues/`.
This folder requires the katconfig repository which is not always available.   
```
Unhandled exception <type 'exceptions.RuntimeError'> : Could not access calibrator catalogue default location
add explicit location of catalogue folder using --cat-path <dirname>
```   
The standard catalogues will be made available in a separate repository for local use, with the updated command   
`python astrokat-cals.py --prop-id 'SCI-20180624FC-01' --pi 'Fernando Camilo' --contact 'fernando@ska.ac.za, sharmila@ska.ac.za' --target 'Abell 13' '00:13:32.2' '-19:30:03.6' --cal-tags gain flux ----cat-path ../calibrators/`

* The generation graphical output such as the elevation plot and PDF report creation is not always available on the observation systems. If the python `matplotlib` package is not available, the script will default to the text output displayed to screen and indicate this to the user with the output:   
```
Required matplotlib functionalities not available
Cannot create elevation plot or generate report
Only producing catalogue file and output to screen
```