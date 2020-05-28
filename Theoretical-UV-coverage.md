Theoretical calculation of UV-coverage given input antenna positions, target equatorial coordinates and start time.
Default is to assume a snap shot duration of 300 seconds sampled at 100 time points equally distributed over the duration.
```
UV coverage calculator for MeerKAT telescope

optional arguments:
  -h, --help            show this help message and exit
  --version             show program's version number and exit
  --config CONFIG       MeerKAT antenna positions YAML (default: None)
  --radec RA DEC        target equatorial coordinates in string format
                        HH:MM:SS.f DD:MM:SS.f (default: None)
  --start START         observation start time, UTC (default: None)
  --duration DURATION   observation duration in seconds (default: 300)
  --steps NTIMESLOTS    number of time slots equally distributed over duration
                        (default: 100)
  --sub [ANT [ANT ...]]
                        list of antenna names in subarray (default: None)
  --natural             UV mask and synthesize beam, natural weighting without
                        a taper (default: False)
  --gaussian            UV mask and synthesize beam, natural weighting with
                        Gaussian taper (default: False)
  -s, --save            save graphs to PNG format (default: False)
  -v, --verbose         display intermittend results and all graphs (default:
                        False)
```

Configuration for the antenna array is specified in an input YAML file.
The file only contains a `reference` position indication to general location of the telescope,
followed by the list of `antennas` relative to the telescope reference position.    
The MeerKAT array configuration file look like:    
```
reference:
  latitude: '-30:42:39.8'
  longitude: '21:26:38.0'
  altitude: 1086.6
antennas:
- name=m000, diameter=13.5, east=-8.264, north=-207.29, up=8.5965
- name=m001, diameter=13.5, east=1.1205, north=-171.762, up=8.4705
- name=m002, diameter=13.5, east=-32.113, north=-224.236, up=8.6445
- name=m003, diameter=13.5, east=-66.518, north=-202.276, up=8.285
- name=m004, diameter=13.5, east=-123.624, north=-252.946, up=8.513
...
```

Default output is a snap shot standard U vs V plot    
```
# J0025-2602 (0023-263)
astrokat-uvcoverage.py --config config/mkat_antennas.yml --steps 10 --duration 300 --radec 00:25:49.17 -26:02:12.8 --start '2013-6-21 21:00:00' -v

# J1939-6342 (1934-638)
astrokat-uvcoverage.py --config config/mkat_antennas.yml --steps 10 --duration 300 --radec 19:39:25.0264 -63:42:45.624 --start '2013-6-21 21:00:00' -v

# J0318+1628 (CTA21)
astrokat-uvcoverage.py --config config/mkat_antennas.yml --steps 10 --duration 300 --radec 03:18:57.77 16:28:33.1 --start '2013-6-21 21:00:00' -v
```

The snap shot map can be compared to the UV-coverage sampling at `100` time slots over the 10-hour observation period for a target at `-30` degree declination.
```
astrokat-uvcoverage.py --config config/mkat_antennas.yml --steps 10 --duration 600 --radec 00:25:49.17 -26:02:12.8 --start '2013-6-21 21:00:00'
```
compared to
```
astrokat-uvcoverage.py --config config/mkat_antennas.yml --steps 100 --duration 36000 --radec 00:25:49.17 -26:02:12.8 --start '2013-6-21 21:00:00'
```

Additional graphs
* UV mask and synthesize beam, natural weighting without a taper by adding the `--natural` argument to the instruction set
* UV mask and synthesize beam, natural weighting with Gaussian taper by adding the `--gaussian` argument to the instruction set

Examples maps generation command 
```
astrokat-uvcoverage.py --config config/mkat_antennas.yml --steps 10 --duration 300 --radec 19:39:25.0264 -63:42:45.624 --start '2013-6-21 21:00:00' --natural --gaussian
```

PSF for only core antennas
```
astrokat-uvcoverage.py --config config/mkat_antennas.yml --steps 10 --duration 300 --radec 19:39:25.0264 -63:42:45.624 --start '2013-6-21 21:00:00' --natural --gaussian --sub m002 m000 m005 m006 m001 m003 m004 m018 m020 m017 m015 m029 m021 m007 m019 m009 m016 m011 m028 m012 m027 m034 m035 m014 m042 m022 m013 m036 m008 m031 m026 m047 m030 m010 m032 m041 m037 m023 m038 m043 m039 m040 m024 m025
```