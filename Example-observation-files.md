The examples provided is in the form of simple observation catalogues converted to observation configuration files.
This is simply introductory, more advance users can construct an observation configuration file during the proposal planning phase using the offline observation functionality   


## Simple calibrator drift scan
An example observation that performs drift scans over calibrators as targets.
Use whatever correlator is active at scheduling, but requiring 2 second dump rates. Noise diode activation before each scan is included for Tsys calibration.
* Input catalogue   
```
1934-638,radec,19:39:25.03,-63:42:45.63
0408-65,radec,04:08:20.38,-65:45:09.1
```
* Convert catalogue to observation configuration file   
```
python catalogue2config.py --catalogue drift_targets.csv --obsfile drift_targets.yaml --dump-rate 2 --noise-source 0.1 -1 --target-duration 180 --drift-scan
```
Noise source will be active for 100ms before each drift-scan
* Output observation configuration file   
```
instrument:
  dump_rate: 0.5
noise_diode:
  pattern: all
  on_fraction: -1.0
  cycle_len: 0.1
observation_loop:
  - LST: 0.0-23.9
    target_list:
      - name=1934-638, radec=19:39:25.03 -63:42:45.63, tags=target, duration=180.0, type=drift_scan
      - name=0408-65, radec=04:08:20.38 -65:45:09.1, tags=target, duration=180.0, type=drift_scan
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
python catalogue2config.py --catalogue image.csv --obsfile image_sim.yaml --target-duration 180 --bpcal-duration 60 --gaincal-duration 30 --bpcal-interval 1800 --product bc856M4k
```
* Output observation configuration file   
```
instrument:
  product: bc856M4k
observation_loop:
  - LST: 11.140-23.248
    target_list:
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=target, duration=180.0
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=target, duration=180.0
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=target, duration=180.0
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=target, duration=180.0
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=target, duration=180.0
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=target, duration=180.0
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=target, duration=180.0
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=target, duration=180.0
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=target, duration=180.0
    calibration_standards:
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal delaycal, duration=60.0, cadence=1800.0
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=bpcal polcal, duration=60.0, cadence=1800.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
```
* Configuration file updated to remove the cadence added to 3C286, which is unwanted   
```
instrument:
  product: bc856M4k
observation_loop:
  - LST: 11.140-23.248
    target_list:
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=target, duration=180.0
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=target, duration=180.0
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=target, duration=180.0
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=target, duration=180.0
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=target, duration=180.0
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=target, duration=180.0
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=target, duration=180.0
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=target, duration=180.0
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=target, duration=180.0
    calibration_standards:
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal delaycal, duration=60.0, cadence=1800.0
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=bpcal polcal, duration=60.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
```
* Second desirable observation setup
```
python catalogue2config.py --catalogue image.csv --obsfile image.yaml --target-duration 180 --bpcal-duration 300 --gaincal-duration 65 --gaincal-interval 600 --bpcal-interval 1800 --product bc856M4
```


## Complex spectral line monitoring observation
Not all observations are straightforward, depending on science need, the observation configuration may be complex with looped observations over LST ranges. It is worthwhile noting that these observations do not have a cadence on the calibration standards, which will cause the standards to be observed directly after the target list.
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
```
python catalogue2config.py --catalogue OH_periodic_masers.csv --obsfile OH_periodic_masers.yaml --target-duration 600 --bpcal-duration 300 --gaincal-duration 60 --product c856M32k
```
* Output observation configuration file   
```
instrument:
  product: c856M32k
observation_loop:
  - LST: 9.448-23.344
    target_list:
      - name=G328.24-0.55, radec=15:54:06.11 -53:50:47.0, tags=B1950 target, duration=600.0
      - name=G331.13-0.24, radec=16:07:10.8 -51:42:29.0, tags=B1950 target, duration=600.0
      - name=G338.93-0.06, radec=16:39:36.5 -46:00:05.0, tags=B1950 target, duration=600.0
      - name=G339.62-0.12, radec=16:42:26.5 -45:31:18.0, tags=B1950 target, duration=600.0
      - name=G351.78-0.54, radec=17:23:20.9 -36:06:46, tags=B1950 target, duration=600.0
      - name=G9.62+0.20, radec=18:03:15.98 -20:31:52.9, tags=B1950 target, duration=600.0
      - name=G12.89+0.49, radec=18:08:56.4 -17:32:14.0, tags=B1950 target, duration=600.0
    calibration_standards:
      - name=1613-586, radec=16:17:17.88951 -58:48:07.8604, tags=gaincal delaycal, duration=60.0
      - name=1722-55, radec=17:26:49.615 -55:29:40.60, tags=gaincal delaycal, duration=60.0
      - name=1730-130, radec=17:33:02.705790 -13:04:49.548230, tags=gaincal delaycal, duration=60.0
      - name=1934-638, radec=19:39:25.026 -63:42:45.63, tags=bpcal fluxcal delaycal, duration=300.0
      - name=3C286, radec=13:31:08.29 +30:30:33.0, tags=bpcal fluxcal delaycal, duration=300.0
```
* Configuration file updated and observation complexity formulated during observation planning runs   
```
instrument:
  product: c856M32k
observation_loop:
  - LST: 10-12
    target_list:
      - name=G328.24-0.55, radec=15:54:06.11 -53:50:47.0, tags=B1950 target, duration=300
      - name=1613-586, radec=16:17:17.88951 -58:48:07.8604, tags=gaincal delaycal, duration=60
      - name=G331.13-0.24, radec=16:07:10.8 -51:42:29.0, tags=B1950 target, duration=300
      - name=G338.93-0.06, radec=16:39:36.5 -46:00:05.0, tags=B1950 target, duration=300
      - name=1722-55, radec=17:26:49.615 -55:29:40.60, tags=gaincal delaycal, duration=60
      - name=G339.62-0.12, radec=16:42:26.5 -45:31:18.0, tags=B1950 target, duration=300
    calibration_standards:
      - name=3C286, radec=13:31:08.29 +30:30:33.0, tags=bpcal fluxcal delaycal, duration=300
  - LST: 12-16
    target_list:
      - name=G328.24-0.55, radec=15:54:06.11 -53:50:47.0, tags=B1950 target, duration=300
      - name=1613-586, radec=16:17:17.88951 -58:48:07.8604, tags=gaincal delaycal, duration=60
      - name=G331.13-0.24, radec=16:07:10.8 -51:42:29.0, tags=B1950 target, duration=300
      - name=G338.93-0.06, radec=16:39:36.5 -46:00:05.0, tags=B1950 target, duration=600
      - name=1722-55, radec=17:26:49.615 -55:29:40.60, tags=gaincal delaycal, duration=60
      - name=G339.62-0.12, radec=16:42:26.5 -45:31:18.0, tags=B1950 target, duration=300
      - name=G351.78-0.54, radec=17:23:20.9 -36:06:46, tags=B1950 target, duration=300
      - name=1730-130, radec=17:33:02.705790 -13:04:49.548230, tags=gaincal delaycal, duration=60
      - name=G9.62+0.20, radec=18:03:15.98 -20:31:52.9, tags=B1950 target, duration=600
      - name=G12.89+0.49, radec=18:08:56.4 -17:32:14.0, tags=B1950 target, duration=600
      - name=1730-130, radec=17:33:02.705790 -13:04:49.548230, tags=gaincal delaycal, duration=60
    calibration_standards:
      - name=3C286, radec=13:31:08.29 +30:30:33.0, tags=bpcal fluxcal delaycal, duration=300
  - LST: 16-23
    target_list:
      - name=G328.24-0.55, radec=15:54:06.11 -53:50:47.0, tags=B1950 target, duration=300
      - name=1613-586, radec=16:17:17.88951 -58:48:07.8604, tags=gaincal delaycal, duration=60
      - name=G331.13-0.24, radec=16:07:10.8 -51:42:29.0, tags=B1950 target, duration=300
      - name=G338.93-0.06, radec=16:39:36.5 -46:00:05.0, tags=B1950 target, duration=600
      - name=1722-55, radec=17:26:49.615 -55:29:40.60, tags=gaincal delaycal, duration=60
      - name=G339.62-0.12, radec=16:42:26.5 -45:31:18.0, tags=B1950 target, duration=300
      - name=G351.78-0.54, radec=17:23:20.9 -36:06:46, tags=B1950 target, duration=300
      - name=1730-130, radec=17:33:02.705790 -13:04:49.548230, tags=gaincal delaycal, duration=60
      - name=G9.62+0.20, radec=18:03:15.98 -20:31:52.9, tags=B1950 target, duration=600
      - name=G12.89+0.49, radec=18:08:56.4 -17:32:14.0, tags=B1950 target, duration=600
      - name=1730-130, radec=17:33:02.705790 -13:04:49.548230, tags=gaincal delaycal, duration=60
    calibration_standards:
      - name=1934-638, radec=19:39:25.026 -63:42:45.63, tags=bpcal fluxcal delaycal, duration=300
```

Note the complex combination of gain calibrators now associated with target positions, with only the bandpass calibrators at cadence given the need to optimise time, minimise atmospheric differences, as well as the assumed stability of the passband over time.

After editing, and before continuing to observation planning, it is always a good idea to see that the configuration file can be parsed successfully. The `readconfig.py` script will take the config file and if parsed successfully, will display the observation strategy described in the file to screen   
`python readconfig.py OH_periodic_masers.yaml`.