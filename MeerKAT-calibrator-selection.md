* For a single target observation or to quickly find calibrators for a target during planning   
`python astrokat-cals.py --target 'Abell 13' '00:13:32.2' '-19:30:03.6' --calibrators gain flux`   
Which will display the selected calibrator instruction to file, as well as generate an elevation display for the target and suggested calibrators.   
```
Observation catalogue for 2018-09-25 10:14:19.361Z
# Observation catalogue for proposal ID None
# PI: None
# Contact details: None
Abell 13, tags=radec target, 0:13:32.20 -19:30:03.6, no flux info
J2348-1631 (2345-167), tags=radec gaincal, 23:48:02.62 -16:31:12.6, no flux info
J0025-2602 (0023-263), tags=radec fluxcal, 0:25:49.17 -26:02:12.8, flux defined for 333 - 99000 MHz


Observation Table for 2018-09-25 10:14:19.361Z
Target          Rise Time       Set Time        Separation Angle
Abell 13        17:19:54Z       17:19:54Z       159.64 deg from Sun
J2348-1631      17:00:52Z       17:00:52Z       6.75 deg from Abell 13
J0025-2602      17:18:24Z       17:18:24Z       7.12 deg from Abell 13
```

* To quickly construct an observation catalogue, targets can be provided in a CSV input catalogue, as well as some  observation related information   
`python astrokat-cals.py --prop-id 'SCI-20180624FC-01' --pi 'Fernando Camilo' --contact 'fernando@ska.ac.za, sharmila@ska.ac.za' --catalogue ../catalogues/Abell13.csv --calibrators gain bandpass --report`   
It will select and add the closest calibrator to each target and create a catalogue CSV file. This file can then be edited by the astronomer before submission or updated should correction be required. Adding the `--report` argument will also generate a PDF file with the summary information shown above.   
```
Observation catalogue ./SCI-20180624FC-01_Abell13.csv
Observation catalogue ./SCI-20180624FC-01_Abell13.pdf
```

* An updated UTC plot of catalogue targets can be shown by adding the `--view` option   
`python astrokat-cals.py --catalogue SCI-20180624FC-01_Abell13.csv --view`   
![source elevation](https://github.com/rubyvanrooyen/astrokat/blob/master/wiki/elevation_utc_lst.png)
