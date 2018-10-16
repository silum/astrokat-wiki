Find the closest calibrators to a target and create a CSV catalogue file.

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