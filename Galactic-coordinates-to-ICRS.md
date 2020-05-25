## Convenience calculator to transform Galactic coordinates to the ICRS frame.

### Convert coordinates of a single galactic target to equatorial    
`astrokat-coords.py --galactic T11R00C02 21h51m30.37s +00d59m15.56s`    
or    
`astrokat-coords.py --galactic T11R00C02 21.8584h 0.9877d`    
or    
`astrokat-coords.py --galactic T11R00C02 327.8760d 0.9877d`    

Will generate output
```
Target T11R00C02
Galactic coordinates
	(21:51:30.2400, +00:59:15.7200) = (21.8584h, 0.9877d) = (327.8760d, 0.9877d)
(RA, DEC) coordinates
	(15:49:32.1367, -53:02:01.4826) = (15.8256h, -53.0337d) = (237.3839d, -53.0337d)
```

For more verbose and file output add the `-v` (`--verbose`) and `--outfile` options
```
astrokat-coords.py --galactic T11R00C02 21.8584h 0.9877d -v --outfile galactic_as_equitorial.csv
```
To obtain output:
```
Target T11R00C02
Galactic coordinates
	(21:51:30.2400, +00:59:15.7200) = (21.8584h, 0.9877d) = (327.8760d, 0.9877d)
(RA, DEC) coordinates
	(15:49:32.1367, -53:02:01.4826) = (15.8256h, -53.0337d) = (237.3839d, -53.0337d)

Equatorial catalogue file created: galactic_as_equitorial.csv
Catalogue file entries
T11R00C02, radec target, 15:49:32.1367, -53:02:01.4826
```
The output CSV catalogue created has the [MeerKAT calibrators and CSV catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrators-and-CSV-catalogues) format.


### Convert the coordinates for galactic targets listed in a CSV catalogue file
Expected format for input galactic target CSV file (`cat sample_targetlist_galactic.csv`)
```
# Catalogue with example galactic coordinates
# name, tag, galactic longitude (l='HMS'), galactic latitude (b='DMS')
Test, gal target, 1h12m43.2s, +1d12m43s
T11R00C02, gal taget, 21h51m30.37s, +00d59m15.56s
```

Generate a MeerKAT observation CSV catalogue containing the equatorial coordinates of input galactic targets.
```
astrokat-coords.py --catalogue sample_targetlist_galactic.csv --outfile sample_targetlist_equatorial.csv -v
```
Will generate output
```
Input file
# Catalogue with example galactic coordinates
# name, gal target, galactic longitude (l='HMS'), galactic latitude (b='DMS')
Test, gal target, 1h12m43.2s, +1d12m43s
T11R00C02, gal taget, 21h51m30.37s, +00d59m15.56s

Equatorial catalogue file created: sample_targetlist_equatorial.csv
Catalogue file entries
Test, radec target, 18:19:39.8143, -12:31:42.8667
T11R00C02, radec target, 15:49:32.3166, -53:02:00.3906
```

### Coordinate representation formats    
Although not the main function, this helper script will also show coordinate format for equatorial input targets.    
Given string (RA, DEC) coordinates will display coordinate in string and floating point formats
```
astrokat-coords.py --radec J1939-6342 19:39:25.0264 -63:42:45.624
astrokat-coords.py --radec J1939-6342 19h39m25.0264s -63d42m45.624s
astrokat-coords.py --radec J1939-6342 294.85427667d -63.71267333d
astrokat-coords.py --radec J1939-6342 19.65695178h -63.71267333d
```
Will generate output
```
Target J1939-6342
(RA, DEC) coordinates
	(19:39:25.0264, -63:42:45.6240) = (19.6570h, -63.7127d) = (294.8543d, -63.7127d)
```
