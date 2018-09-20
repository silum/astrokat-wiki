# Time calculations providing LST information relative to the MeerKAT telescope

* Current LST at MeerKAT telescope: 
`python astrokat-lst.py`
> Current clock times at MeerKAT:   
> Now is 2018/9/20 11:19:21Z UTC and 12:42:19.57 LST

* Return MeerKAT LST for a given UTC date and time   
`python mkat_lst.py --utc '2018-08-06 12:34'`   
**MeerKAT LST at 2018/8/6 12:34:00z is 10:59:45.92**

* Calculate per target LST   
`python mkat_lst.py --target '17:22:27.46877 -38:12:09.4023'`   
**Target +17:22:27.46877 -38:12:09.4023 LST range 11:32:34.24 to 23:14:53.66**

* Simple tool to figure out when an observation will start given the LST hour   
`python mkat_lst.py --lst 10`   
**Your observation should start at 2018-08-07 11:30 UTC**   
or   
`python mkat_lst.py --lst 10 --utc '2018-08-06 00:00'`   
**Your observation should start at 2018-08-06 11:34 UTC**

* Display distribution of selected targets in catalogue file over MeerKAT LST   
`python mkat_lst.py --catalogue ../catalogues/OH_periodic_masers.csv`   
![source elevation](https://github.com/rubyvanrooyen/astrokat/blob/master/wiki/elevation_lst.png)

