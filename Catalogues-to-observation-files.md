## Observation catalogue
The _minimum required information_ for any observation is a list of observation targets specified as one target per line, using comma separated formatting to provide the relevant target information.   
Per target required information:   
`[name], tags, ra, dec` or `[name], tags, az, el` or `[name], tags, l, b`   
Details discussion of the per target information can be found on the [Observation target specification](https://github.com/rubyvanrooyen/astrokat/wiki/Observation-target-specification) page

The targets can be celestial, horizontal or galactic. Specials such as TLE for satellites and near earth objects are not currently available.

Generally a catalogue is expected as part of the observation request. See [docs](https://github.com/rubyvanrooyen/astrokat/tree/master/docs) for an example observation request template


## Converting target catalogue to observation configuration file
If an observation catalogue file is provided, an initial configuration file can easily be generated.
More extensive information on the observation configuration file can be found on the [Observation configuration file](https://github.com/rubyvanrooyen/astrokat/wiki/Observation-configuration-file) page

The `catalogue2obsfile.py` script does simple conversion of existing observation catalogues, CSV format, to configuration files, YAML format.
```
python catalogue2obsfile.py -h
```
The only required input parameter is the name of the catalogue file, `--catalogue`.   
For convenience the output will be displayed to screen if an output filename, `--obsfile`, is not specified.  
Once the user is satisfied with the output, an observation profile can be created   
```
python catalogue2obsfile.py --catalogue targets.csv --obsfile targets.yaml ...
```

### Adding observation source targets
Basic steps for conversion can be illustrated using some random targets, `targets.csv`
```
, radec target, 0, -90
, azel target, 10, 50
, gal target, -10, 40
```

Simple convert targets to configuration
```
python catalogue2obsfile.py --catalogue astrokat/tests/test_convert/targets.csv --target-duration 10
```
Resulting in a basic target list
```
observation_loop:
  - LST: 0.000-23.9
    target_list:
      - name=target0_radec, radec=0 -90, tags=target, duration=10.0
      - name=target1_azel, azel=10 50, tags=target, duration=10.0
      - name=target2_gal, gal=-10 40, tags=target, duration=10.0
```
No _`LST`_ range was specified in the conversion, so the range over which the targets are visible has been inserted by default.   
Target names were not provided, resulting in generated names to be created.   
Each target has its own _`tags`_ and _`duration`_, which can be updated by the user.

The target list construction does not distinguish between targets in calibrators when converting from CSV format catalogues to observation files, but when converting catalogues that contains calibrators, the user must specify the associated timings required.
```
python catalogue2obsfile.py --catalogue astrokat/tests/test_convert/two_calib.csv  --primary-cal-duration 180

observation_loop:
  - LST: 0.0-23.9
    target_list:
      - name=1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal, duration=180.0
      - name=0408-65, radec=04:08:20.38 -65:45:09.1, tags=bpcal, duration=180.0
```
In general the default application assumes _`gaincal`_ as secondary calibrators, while _`bpcal`_, _`fluxcal`_ and _`polcal`_ targets are considered primary calibrators.


### Observations with targets and calibrators
Gain calibrators are generally intermingled with the source target list, with primary calibrators often need only be visited at some slower cadence.
```
7th set of pointins of the Galactic plane Mosaic
J1939-6342 | *1934-638, radec bpcal delaycal, 19:39:25.03, -63:42:45.63
J1331+3030 | *3C286, radec bpcal polcal, 13:31:08.288, +30:30:32.959
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

For this example we assume a bandpass calibrator to be observed for 30 seconds every hour. Two calibrators are added since the targets fill the whole 24h LST range.
An example is an imaging observation with both primary and secondary calibrators, where the bandpass calibrator is only observed every 30 minutes for 3 minutes.

While the target and gain calibrator, without cadence values will be observed in sequence as listed in the catalogue. The targets for 5 minutes and the gain calibrators for 65 seconds.
```
python catalogue2obsfile.py --catalogue astrokat/tests/test_convert/image.csv --target-duration 300 --primary-cal-duration 180 --primary-cal-cadence 1800 --secondary-cal-duration 65

#7th set of pointins of the Galactic plane Mosaic
observation_loop:
  - LST: 11.139-23.248
    target_list:
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal delaycal, duration=180.0, cadence=1800.0
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=bpcal polcal, duration=180.0, cadence=1800.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=65.0
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=target, duration=300.0
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=target, duration=300.0
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=target, duration=300.0
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=target, duration=300.0
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=target, duration=300.0
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=target, duration=300.0
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=target, duration=300.0
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=target, duration=300.0
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=target, duration=300.0
```


### Adding telescope specific requirements
Most astronomy observations will require a specific observation instrument, provided by MeerKAT as correlator products. These are generally identified from the science proposal requirements, but if known upfront, can be added in the conversion step.
```
python catalogue2obsfile.py --catalogue astrokat/tests/test_convert/image.csv --target-duration 300 --primary-cal-duration 180 --primary-cal-cadence 1800 --secondary-cal-duration 65 --product c856M4k --band l --integration-period 8 --max-duration 11700

#7th set of pointins of the Galactic plane Mosaic
instrument:
  integration_period: 8.0
  band: l
  product: c856M4k
durations:
  obs_duration: 11700.0
observation_loop:
  - LST: 11.139-23.248
    target_list:
      - name=J1939-6342 | *1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal delaycal, duration=180.0, cadence=1800.0
      - name=J1331+3030 | *3C286, radec=13:31:08.288 +30:30:32.959, tags=bpcal polcal, duration=180.0, cadence=1800.0
      - name=1827-360, radec=18:30:58.80 -36:02:30.1, tags=gaincal, duration=65.0
      - name=T3R04C06, radec=+17:22:27.46877 -38:12:09.4023, tags=target, duration=300.0
      - name=T4R00C02, radec=+17:11:22.47016 -37:51:51.0758, tags=target, duration=300.0
      - name=T4R00C04, radec=+17:08:23.04449 -38:39:29.8486, tags=target, duration=300.0
      - name=T4R00C06, radec=+17:05:19.53524 -39:26:50.4693, tags=target, duration=300.0
      - name=T4R01C01, radec=+17:14:51.97986 -37:45:16.2459, tags=target, duration=300.0
      - name=T4R01C03, radec=+17:11:55.13096 -38:33:15.4802, tags=target, duration=300.0
      - name=T4R01C05, radec=+17:08:54.27808 -39:20:57.3978, tags=target, duration=300.0
      - name=T4R02C02, radec=+17:15:26.58923 -38:26:36.9760, tags=target, duration=300.0
      - name=T4R02C04, radec=+17:12:28.40227 -39:14:39.4421, tags=target, duration=300.0
```