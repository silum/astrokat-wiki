## Convert CSV format target catalogue to observation configuration file
The minimum requirement with a proposal is a comma separated file that contains a list of all targets, with or without calibrators. This is a simple text file with defined format:   
`[name], tags, ra, dec` or `[name], tags, az, el` or `[name], tags, l, b`

The targets can be celestial, horizontal or galactic. Specials such as TLE for satellites and near earth objects are not currently available.

The basic steps for easy conversion:
* Input catalogue of random targets
```
, radec target, 0, -90
, azel target, 10, 50
, gal target, -10, 40
```
* Convert catalogue to observation configuration file   
```
python catalogue2config.py -i ../catalogues/targets.csv -o targets.yaml --instrument c856M4k --target-duration 10
```
* Output YAML file --- alternative to the catalogue, astronomers may prefer to provide the observation configuration file generated during the proposal planning phase using the offline observation functionality   
```
instrument: c856M4k
observation_loop:
  - LST: 0-23
    target_list:
      - name=, radec=0 -90, tags=radec target, duration=10
      - name=, azel=10 50, tags=azel target, duration=10
      - name=, gal=-10 40, tags=gal target, duration=10
    calibration_standards:
```

## A standard imaging observation
Consists of a number of target pointings and one or more calibrators
* Input catalogue   
```
# 7th set of pointings of the Galactic plane Mosaic
J1939-6342 | *1934-638,radec bpcal delaycal, 19:39:25.03,-63:42:45.63
J1331+3030 | *3C286, radec bpcal polcal,13:31:08.288,+30:30:32.959
1827-360, radec gaincal, 18:30:58.80, -36:02:30.1
T3R04C06, radec target, +17:22:27.46877, -38:12:09.4023
T4R00C02, radec target, +17:11:22.47016, -37:51:51.0758
T4R00C04, radec target, +17:08:23.04449, -38:39:29.8486
T4R00C06, radec target, +17:05:19.53524, -39:26:50.4693
T4R01C01, radec target, +17:14:51.97986, -37:45:16.2459
T4R01C03, radec target, +17:11:55.13096, -38:33:15.4802
T4R01C05, radec target, +17:08:54.27808, -39:20:57.3978
T4R02C02, radec target, +17:15:26.58923, -38:26:36.9760
T4R02C04, radec target, +17:12:28.40227, -39:14:39.4421
```
* Convert catalogue to observation configuration file   
```
python catalogue2config.py -i ../catalogues/image.csv -o image.yaml --target-duration 180 --cal-duration 300
```
* Output observation configuration file   
```
instrument: bc856M4k
observation_loop:
  - LST: 11-23
    target_list:
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=radec target, duration=180
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=radec target, duration=180
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=radec target, duration=180
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=radec target, duration=180
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=radec target, duration=180
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=radec target, duration=180
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=radec target, duration=180
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=radec target, duration=180
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=radec target, duration=180
    calibration_standards:
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=radec bpcal delaycal, duration=300
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=radec bpcal polcal, duration=300
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=radec gaincal, duration=300
```
* Minimal edit to file to adjust gain calibrator duration   
```
instrument: bc856M4k
observation_loop:
  - LST: 11-23
    target_list:
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=radec target, duration=180
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=radec target, duration=180
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=radec target, duration=180
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=radec target, duration=180
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=radec target, duration=180
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=radec target, duration=180
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=radec target, duration=180
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=radec target, duration=180
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=radec target, duration=180
    calibration_standards:
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=radec gaincal, duration=65
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=radec bpcal delaycal, duration=300
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=radec bpcal polcal, duration=300
```



## Complex spectral line monitoring observation
Not all observations are straight forward, depending on science need, the observation configuration may be complex with looped observations over LST ranges.
* Input catalogue   
```
G328.24-0.55, radec B1950, 15:54:06.11, -53:50:47.0
G331.13-0.24, radec B1950, 16:07:10.8, -51:42:29.0
G338.93-0.06, radec B1950, 16:39:36.5, -46:00:05.0
G339.62-0.12, radec B1950, 16:42:26.5, -45:31:18.0
G351.78-0.54, radec B1950, 17:23:20.9, -36:06:46
G9.62+0.20, radec B1950, 18:03:15.98,  -20:31:52.9
G12.89+0.49, radec B1950, 18:08:56.4, -17:32:14.0
1613-586, radec gaincal delaycal, 16:17:17.88951,-58:48:07.8604
1722-55, radec gaincal delaycal, 17:26:49.615,-55:29:40.60
1730-130, radec gaincal delaycal, 17:33:02.705790,-13:04:49.548230
1934-638 , radec bpcal fluxcal delaycal, 19:39:25.026 , -63:42:45.63
3C286, radec bpcal fluxcal delaycal, 13:31:08.29,  +30:30:33.0
```
* Convert catalogue to observation configuration file   
* Output observation configuration file   
* Configuration file setup during observation planning runs   
