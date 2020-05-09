

> python astrokat-coords.py --radec J1939-6342 19:39:25.0264 -63:42:45.624
> python astrokat-coords.py --radec J1939-6342 19h39m25.0264s -63d42m45.624s
> python astrokat-coords.py --radec SCP 0 -90

> python astrokat-coords.py --radec J1939-6342 19:39:25.0264 -63:42:45.624 --galactic T11R00C02 21h51m30.37s +00d59m15.56s


python astrokat-coords.py --radec J1939-6342 19:39:25.0264 -63:42:45.624 --galactic T11R00C02 21h51m30.37s +00d59m15.56s --catalogue devel/sample_targetlist_galactic.csv

> cat devel/sample_targetlist_galactic.csv
```
# Catalogue with example galactic coordinates
# name, gal target, galactic longitude (l='HMS'), galactic latitude (b='DMS')
Test, gal target, 1h12m43.2s, +1d12m43s
T11R00C02, gal taget, 21h51m30.37s, +00d59m15.56s
# name, radec target, right ascension, declination
Test, radec target, 00:42:44.2992, 41:16:09.012
J1939-6342, radec target, 19:39:25.0264, -63:42:45.624
```



> python astrokat-coords.py --radec J1939-6342 19:39:25.0264 -63:42:45.624 --galactic T11R00C02 21h51m30.37s +00d59m15.56s --catalogue devel/sample_targetlist_galactic.csv -v
Target J1939-6342
(RA, DEC) coordinates
	(19:39:25.0264, -63:42:45.6240) = (294.8543, -63.7127) degrees
Target T11R00C02
Galactic coordinates
	(21:51:30.3700, +00:59:15.5600) = (327.8765, 0.9877) degrees
(RA, DEC) coordinates
	(15:49:32.3166, -53:02:00.3906) = (237.3847, -53.0334) degrees

Input file
# Catalogue with example galactic coordinates
# name, gal target, galactic longitude (l='HMS'), galactic latitude (b='DMS')
Test, gal target, 1h12m43.2s, +1d12m43s
T11R00C02, gal taget, 21h51m30.37s, +00d59m15.56s
# name, radec target, right ascension, declination
Test, radec target, 00:42:44.2992, 41:16:09.012
J1939-6342, radec target, 19:39:25.0264, -63:42:45.624

Catalogue file created
Test, radec target, 18:19:39.8143, -12:31:42.8667
T11R00C02, radec target, 15:49:32.3166, -53:02:00.3906
Test, radec target, 00:42:44.2992, +41:16:09.0120
J1939-6342, radec target, 19:39:25.0264, -63:42:45.6240
