# Time calculations providing LST information relative to the MeerKAT telescope

* Current LST at MeerKAT telescope:   
`python astrokat-lst.py`
> Current clock times at MeerKAT:   
> Now is 2018/9/20 11:19:21Z UTC and 12:42:19.57 LST

* Return MeerKAT LST for a given UTC date and time   
`python astrokat-lst.py --utc '2018-08-06 12:34'`   
> At 2018/8/6 12:34:00Z MeerKAT LST will be 10:59:45.92

* Calculate per target LST   
`python astrokat-lst.py --target 17:22:27.46877 -38:12:09.4023`   
> Target (17:22:27.46877 -38:12:09.4023) rises at LST 11:32:33.31 and sets at LST 23:14:52.82

* Simple tool to figure out when an observation will start given the LST hour   
`python astrokat-lst.py --lst 10.6 --utc 2018-08-06`   
> 2018-08-06 10.6 LST corresponds to 2018-08-06 12:11:00Z UTC   
