
Simple observation of 3 calibrations, starting at 2018-12-07 05:00:00 UTC and repeating observation of targets for 2 hours.
```
durations:
  start_time: 2018-12-07 05:00:00
  obs_duration: 7200.0
observation_loop:
  - LST: 11:01-15:30
    target_list:
      - name=J0408-6545 | 0408-658, radec=4:08:20.38 -65:45:09.6, tags=bpcal fluxcal, duration=600.0, cadence=3600.0, model=(145.0 18000.0 -0.979 3.366 -1.122 0.0861)
      - name=J1744-5144 | 1740-517, radec=17:44:25.47 -51:44:43.1, tags=gaincal, duration=120.0, model=(145.0 99000.0 -3.694 2.745 -0.3655 -0.015)
      - name=J1939-6342 | 1934-638, radec=19:39:25.05 -63:42:43.6, tags=bpcal fluxcal, duration=600.0, cadence=3600.0, model=(408.0 8640.0 -30.77 26.49 -7.098 0.6053)
```

Multiple pointing observation to be executed at 2019-02-11 02:10:47 UTC.
Targets are observed in sequences listed, with gain calibrator every 12 or so minutes in between targets.
Bandpass and polarisation calibrators gets observed at the start and then again every 30 minutes for bandpass calibrator and every hour for polarisation calibrator.
Since an observation duration, `obs_duration` has not been specified, all targets will only be observed once until the last target.
```
instrument:
  product: c856M4k
  band: l
  integration_period: 8
durations:
  start_time: 2019-02-11 02:10:47
observation_loop:
  - LST: 11:10-23:00
    target_list:
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=target, duration=180.0
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=target, duration=180.0
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=target, duration=180.0
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=target, duration=180.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=target, duration=180.0
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=target, duration=180.0
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=target, duration=180.0
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=target, duration=180.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=target, duration=180.0
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal, duration=60.0, cadence=1800.0
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=polcal, duration=20.0, cadence=3600.0
```

Updating the imaging observation by adding an observation duration, means that the list of targets/gain calibrators will be repeated for as long as time is remaining.
Bandpass and polarisation calibrators observations remains the same, with the primary calibrators getting observed at the start and then again every 30 minutes for bandpass calibrator and every hour for polarisation calibrator.
Removing the instrument specific setup information will not change the behaviour of the script.
```
durations:
  start_time: 2019-02-11 02:10:47
  obs_duration: 3600
observation_loop:
  - LST: 11:10-23:00
    target_list:
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=target, duration=180.0
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=target, duration=180.0
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=target, duration=180.0
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=target, duration=180.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=target, duration=180.0
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=target, duration=180.0
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=target, duration=180.0
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=target, duration=180.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=30.0
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=target, duration=180.0
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal, duration=60.0, cadence=1800.0
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=polcal, duration=20.0, cadence=3600.0
```

An example observation that performs drift scans over calibrators as targets.
Use whatever correlator is active at scheduling, but requiring 2 second dump rates. Noise diode activation before each scan is included for Tsys calibration.
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
  - LST: 0:00-23:50
    target_list:
      - name=1934-638, radec=19:39:25.03 -63:42:45.63, tags=target, duration=180.0, type=drift_scan
      - name=0408-65, radec=04:08:20.38 -65:45:09.1, tags=target, duration=180.0, type=drift_scan
```

## Complex spectral line monitoring observation
Not all observations are straightforward, depending on science need, the observation configuration may be complex with looped observations over LST ranges. It is worthwhile noting that these observations do not have a cadence on the calibration standards, which will cause the standards to be observed directly after the target list.
```
instrument:
  product: c856M32k
observation_loop:
  - LST: 10:00-12:00
    target_list:
      - name=G328.24-0.55, radec=15:54:06.11 -53:50:47.0, tags=B1950 target, duration=300
      - name=1613-586, radec=16:17:17.88951 -58:48:07.8604, tags=gaincal delaycal, duration=60
      - name=G331.13-0.24, radec=16:07:10.8 -51:42:29.0, tags=B1950 target, duration=300
      - name=G338.93-0.06, radec=16:39:36.5 -46:00:05.0, tags=B1950 target, duration=300
      - name=1722-55, radec=17:26:49.615 -55:29:40.60, tags=gaincal delaycal, duration=60
      - name=G339.62-0.12, radec=16:42:26.5 -45:31:18.0, tags=B1950 target, duration=300
      - name=3C286, radec=13:31:08.29 +30:30:33.0, tags=bpcal fluxcal delaycal, duration=300
  - LST: 12:00-16:00
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
      - name=3C286, radec=13:31:08.29 +30:30:33.0, tags=bpcal fluxcal delaycal, duration=300
  - LST: 16:00-23:00
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
      - name=1934-638, radec=19:39:25.026 -63:42:45.63, tags=bpcal fluxcal delaycal, duration=300
```

Note the complex combination of gain calibrators now associated with target positions, with only the bandpass calibrators at cadence given the need to optimise time, minimise atmospheric differences, as well as the assumed stability of the passband over time.