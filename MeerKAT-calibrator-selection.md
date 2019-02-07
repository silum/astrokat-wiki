# Aim
User tool to find the closest, good calibrators to a target, as well as create a CSV catalogue file.   
`astrokat-cals.py -h`

```
usage: astrokat-cals.py [options]

calibrator selection for MeerKAT telescope

optional arguments:
  -h, --help            show this help message and exit
  --version             show program's version number and exit
  --pi PI               name of principle investigator or designated project
                        leader (default: None)
  --contact CONTACT     PI contact details, such as email address or phone
                        number (default: None)
  --prop-id PROP_ID     proposal ID (default: None)
  --cal-tags <tag> [<tag> ...]
                        list of tags specifying types of calibrators to
                        provide: gain bp flux pol (default: None)
  --cat-path CAT_PATH   path to calibrator catalogue folder (default: None)
  --solar-angle SOLAR_ANGLE
                        solar separation angle from target observation region
                        (default: 20.0)
  --datetime DATETIME   catalogue creation or viewing date and time with
                        string format 'YYYY-MM-DD HH:MM' (default: 2019-02-06
                        15:04:40.691558)
  --horizon HORIZON     minimum pointing angle of MeerKAT dish (default: 17.0)
  --lst                 display rise and set times in LST (default UTC)
                        (default: False)

observation target specification (*required*):
  multiple targets are added using an input file, while for a single target
  a quick command line option is also available -- simultaneous use of a
  catalogue and input target is not allowed.

  --infile INFILE       observation targets as CSV input file (default: None)
  --target Name RA Decl
                        returns MeerKAT LST range for a celestial target with
                        coordinates HH:MM:SS DD:MM:SS (default: None)
  --view CATALOGUE      display catalogue sources elevation over time
                        (default: None)

catalogue output options:
  options to view constructed observation catalogue

  --outfile OUTFILE     path and name for observation catalogue CSV file (if
                        not provided, only target listing will be displayed)
                        (default: None)
  --text-only           output observation target information text only
                        (default: False)
  --all-cals            show all primary calibrators in catalogue (default:
                        False)
  --view-tags <tag> [<tag> ...]
                        list of plot options for target visualization:
                        elevation solarangle riseset (default: elevation)
```

#### Basic functionality
The tool requires that a target, or a catalogue file be specified.   
A single target is specified with the `--target` argument,    
while catalogue file containing multiple targets are provided with the `--infile` argument.   
* Format for specifying a single target is simply `--target <name> <ra> <dec>`, currently only string format input for the (RA, DEC) coordinates are implemented, `HH:MM:SS.f DD:MM:SS.f`   

* In addition to the targets, the user must also specify the type of calibrators to select using the `--cal-tags` argument. Current available options are `gain, bp, flux, or pol`

| Tag | Calibrator Type |
| --- | --- |
| gain | select a gain calibrator|
| bp | add bandpass calibrator(s)|
| flux | add flux calibrator(s)|
| pol | select a polarisation calibrator|

#### Additional information
* Calibrators are selected using observatory calibrator catalogues located in `katconfig/user/catalogues`.   
To use a user specified catalogue the `--cat-path` argument must be specified giving the full path to the folder where the desired catalogues are located.   
**Important**: The reader should note that the current implementation of `astrokat-cals.py` assume a specific calibrator naming convension:   
`Lband-<calibrator_calssification>-calibrators.csv`.   
The implementation will be updated as new requirements are identified based on usage.

* Optional input arguments such as `--pi`, `--contact` and `--prop-id`, provides metadata that is added when the observation catalogue file is created.
The purpose of this information is simply informational and is supplementary to the target specification. 

* MeerKAT standard catalogues have a large number of gain calibrators available, but currently only a small number of primary calibrators such as bandpass, flux and polarisation are available. Thus, `astrokat-cals.py` will find the _closest gain calibrator_ to the target, the _closet bandpass and flux calibrators_, as well as _additional bandpass and flux calibrators_ to cover the entire LST time range that the target will be visible to ensure a primary calibrator is always visible.

* Currently, MeerKAT only has three flux calibrators with known flux models, and three polarisation calibrators. The number of calibrators will improve going forward, but sometimes to make selection between these few calibrators visually, add the `--all-cals` tag. This will display all calibrators in the selected catalogues (flux or polarisation) in the elevation plot. 

* While the target elevation plot is the default display, for more intricate observations, more display options are available
| Tag | Calibrator Type |
| --- | --- |
| elevation | Elevation angle or catalogue sources over LST range for a 24hr period|
| solarangle | Solar separation angle of target over a year|
| riseset | Target rise and set times (UTC) over the duration of a year|


# Detailed examples

## Creating a catalogue

### Using a single target as input
Most basic implementation is to view available calibrators for a target   
`astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain bp flux --lst`

Note the use of the `--lst` tag, this is generally for planning when catalogues are constructed and calibrators has to be selected.

Resulting in display output:
```
Observation Table for 2019/2/7 04:37:29 (UTC)
Times listed in LST for target rise and set times
Target visible when above 17.0 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     19:19:50.89     7:58:10.41      60.75           separation from Sun
J0010-4153      radec bpcal                     0:10:52.52      -41:53:10.8     17:54:38.67     6:28:57.07      16.13 ***       separation from NGC641_03D03
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     19:42:01.46     8:10:46.03      3.72
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     20:11:15.45     12:05:43.79     31.01 ***
```
With the `***` a visual aid to the user to draw attention that the closest calibrator found was more than 15 degrees away from the target.

If the Python `matplotlib` library is available, an elevation graph will also be displayed.
![astrokat-cals-single-target](https://github.com/ska-sa/astrokat/blob/master/wiki/astrokat-cals-single-target.png)   

Useful additional options:
* To create a catalogue file a filename for the output catalogue has to be provided by adding the `--outfile` argument.   
```
astrokat-cals.py --prop-id 'SCI-dateinitials-nr' --pi 'No One' --contact 'dummy@ska.ac.za' --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain bp flux --outfile test_NGC641_03D03.csv
```
`Observation catalogue test_NGC641_03D03.csv`   
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


## Viewing a catalogue
An updated elevation plot of catalogue targets can be shown by adding the `--view` option   
* `python astrokat-cals.py --view mosaic_catalogue.csv`   
* `python astrokat-cals.py --view mosaic_catalogue.csv --datetime '2018-04-06 12:34'`
* `python astrokat-cals.py --view mosaic_catalogue.csv --text-only --solar-angle=55 --datetime '2018-04-06 12:34'`


### For multiple targets in an input file
* Providing multiple targets in an input file simply follows the typical target specification definition of the observation [CSV catalogue](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrators-and-CSV-catalogues) files.
Default is to select the first target in the list of targets to find the indicated calibrators for. This is not always desirable and to indicate which targets in the file to use when selecting calibrators the `calref` tag must be added to that target's observation information.   
Example of such an input file:
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

Some observations, such as the example mosaic observations only require single calibrators and for such observations simple CSV files listing the targets to observe can be used.    
The only difference is the addition of a unique tag, _`calref`_, to indicate which target or coordinate should be used when selected the closest calibrator sources.
```
# AR1 mosaic NGC641
# Catalogue for the AR1 mosaic tests
NGC641_02D02, radec target, 01:39:25.009, -42:14:49.216
NGC641_03D02, radec target, 01:37:01.491, -42:14:49.216
NGC641_02D03, radec target, 01:40:36.768, -42:37:41.000
NGC641_03D03, radec target calref, 01:38:13.250, -42:37:41.000
NGC641_04D03, radec target, 01:35:49.732, -42:37:41.000
NGC641_02D04, radec target, 01:39:25.009, -43:00:32.784
NGC641_03D04, radec target, 01:37:01.491, -43:00:32.784
```

Using the example input file above listing multiple galactic pointings   
```
astrokat-cals.py --cal-tags gain bp flux --outfile mosaic_catalogue.csv --infile sample_targetlist_for_cals.csv
```

Producing screen output:   
```
Observation catalogue mosaic_catalogue.csv

Observation Table for 2018/11/14 11:32:31
Times in UTC when target is above the default horizon = 20 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_02D02    radec target                    1:39:25.01      -42:14:49.2     14:39:36        02:38:09        115.45          separation from Sun
NGC641_02D03    radec target                    1:40:36.77      -42:37:41.0     14:39:52        02:40:15        115.21
NGC641_02D04    radec target                    1:39:25.01      -43:00:32.8     14:37:46        02:39:58        114.77
NGC641_03D02    radec target                    1:37:01.49      -42:14:49.2     14:37:13        02:35:46        115.26
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     14:37:30        02:37:52        115.01
NGC641_03D04    radec target                    1:37:01.49      -43:00:32.8     14:35:23        02:37:35        114.58
NGC641_04D03    radec target                    1:35:49.73      -42:37:41.0     14:35:07        02:35:29        114.82
J0010-4153      radec bpcal                     0:10:52.52      -41:53:10.8     13:12:18        01:09:07        16.13 ***       separation from NGC641_03D03
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     14:59:06        02:50:56        3.72
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     15:46:16        06:27:16        31.01 ***
```
Creates the catalogue file:   
```
# AR1 mosaic NGC641
# Catalogue for the AR1 mosaic tests
# Observation catalogue for proposal ID None
# PI: None
# Contact details: None
J0010-4153 | 0008-421, radec bpcal, 0:10:52.52, -41:53:10.8, (145.0 20000.0 -16.93 15.39 -4.21 0.3496)
J0155-4048 | 0153-410, radec gaincal, 1:55:37.06, -40:48:42.4
J0408-6545 | 0408-658, radec bpcal fluxcal, 4:08:20.38, -65:45:09.6, (145.0 18000.0 -0.979 3.366 -1.122 0.0861)
NGC641_02D02, radec target, 1:39:25.01, -42:14:49.2
NGC641_02D03, radec target, 1:40:36.77, -42:37:41.0
NGC641_02D04, radec target, 1:39:25.01, -43:00:32.8
NGC641_03D02, radec target, 1:37:01.49, -42:14:49.2
NGC641_03D03, radec target, 1:38:13.25, -42:37:41.0
NGC641_03D04, radec target, 1:37:01.49, -43:00:32.8
NGC641_04D03, radec target, 1:35:49.73, -42:37:41.0
```
Elevation plot:
![astrokat-cals-multiple-target](https://github.com/ska-sa/astrokat/blob/master/wiki/astrokat-cals-multiple-target.png)

If the `--datetime` argument is not specified, the current time will be used for all time conversions and elevation calculations. The impact of the `--datetime` argument can be shown in the following examples, using the 55 minimum solar separation angle threshold, `--solar-angle`, to highlight the differences:

```
python astrokat-cals.py --cal-tags gain bp flux --infile sample_targetlist_for_cals.csv --datetime '2018-04-06 12:34' --text-only
```
```
Observation Table for 2018/4/6 12:34:00
Times in UTC when target is above the default horizon = 20 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_02D02    radec target                    1:39:25.01      -42:14:49.2     05:12:22        17:10:57        49.50           separation from Sun
NGC641_02D03    radec target                    1:40:36.77      -42:37:41.0     05:12:39        17:13:03        49.92
NGC641_02D04    radec target                    1:39:25.01      -43:00:32.8     05:10:33        17:12:46        50.25
NGC641_03D02    radec target                    1:37:01.49      -42:14:49.2     05:09:59        17:08:34        49.41
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     05:10:16        17:10:40        49.83
NGC641_03D04    radec target                    1:37:01.49      -43:00:32.8     05:08:10        17:10:23        50.15
NGC641_04D03    radec target                    1:35:49.73      -42:37:41.0     05:07:53        17:08:17        49.74
J0010-4153      radec bpcal                     0:10:52.52      -41:53:10.8     03:45:05        15:41:54        16.13 ***       separation from NGC641_03D03
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     05:31:52        17:23:44        3.72
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     06:19:00        21:00:05        31.01 ***
J1939-6342      radec bpcal fluxcal             19:39:25.05     -63:42:43.6     22:04:06        12:23:00        52.49 ***
```
Compared to the output by specifying a required solar separation angle larger than the default 20 degrees.   
```
python astrokat-cals.py --cal-tags gain bp flux --infile sample_targetlist_for_cals.csv --datetime '2018-04-06 12:34' --solar-angle=55 --text-only
```
```
Observation Table for 2018/4/6 12:34:00
Times in UTC when target is above the default horizon = 20 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_02D02    radec target                    1:39:25.01      -42:14:49.2     05:12:22        17:10:57        49.50 ***       separation from Sun
NGC641_02D03    radec target                    1:40:36.77      -42:37:41.0     05:12:39        17:13:03        49.92 ***
NGC641_02D04    radec target                    1:39:25.01      -43:00:32.8     05:10:33        17:12:46        50.25 ***
NGC641_03D02    radec target                    1:37:01.49      -42:14:49.2     05:09:59        17:08:34        49.41 ***
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     05:10:16        17:10:40        49.83 ***
NGC641_03D04    radec target                    1:37:01.49      -43:00:32.8     05:08:10        17:10:23        50.15 ***
NGC641_04D03    radec target                    1:35:49.73      -42:37:41.0     05:07:53        17:08:17        49.74 ***
J0010-4153      radec bpcal                     0:10:52.52      -41:53:10.8     03:45:05        15:41:54        16.13 ***       separation from NGC641_03D03
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     05:31:52        17:23:44        3.72
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     06:19:00        21:00:05        31.01 ***
J1939-6342      radec bpcal fluxcal             19:39:25.05     -63:42:43.6     22:04:06        12:23:00        52.49 ***
```

An optional extra output is a PDF report containing the elevation graph and target information will be generated by adding the `--report` argument:   
```
python astrokat-cals.py --cal-tags gain bp pol flux delay --outfile mosaic_catalogue.csv --infile sample_targetlist_for_cals.csv --solar-angle=55 --datetime '2018-04-06 12:34' --report
```     
Observation catalogue report [mosaic_catalogue.pdf](https://github.com/ska-sa/astrokat/blob/master/wiki/mosaic_catalogue.pdf)



# Important notes to user

* The default folder for the standard calibrator catalogues are `katconfig/user/catalogues/`.
This folder requires the katconfig repository which is not always available.   
```
Unhandled exception <type 'exceptions.RuntimeError'> : Could not access calibrator catalogue default location
add explicit location of catalogue folder using --cat-path <dirname>
```   
The standard catalogues will be made available in a separate repository for local use, with the updated command   
`python astrokat-cals.py --prop-id 'SCI-dateinitials-nr' --pi 'No One' --contact 'dummy@ska.ac.za' --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain bp flux --outfile test_NGC641_03D03.csv --cat-path ../calibrators/`   

* The generation graphical output such as the elevation plot and PDF report creation is not always available on the observation systems. If the python `matplotlib` package is not available, the script will default to the text output displayed to screen and indicate this to the user with the output:   
```
Required matplotlib functionalities not available
Cannot create elevation plot or generate report
Only producing catalogue file and output to screen
```

