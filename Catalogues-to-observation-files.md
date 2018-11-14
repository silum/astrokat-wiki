## Observation catalogue
The _minimum required information_ for any observation is a list of observation targets specified as one target per line, using comma separated formatting to provide the relevant target information.   
Per target required information:   
`[name], tags, ra, dec` or `[name], tags, az, el` or `[name], tags, l, b`   
Details discussion of the per target information can be found on the [Observation target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification) page

The targets can be celestial, horizontal or galactic. Specials such as TLE for satellites and near earth objects are not currently available.

Generally a catalogue is expected as part of the observation request. See [docs](https://github.com/ska-sa/astrokat/tree/master/docs) for an example observation request template


## Converting target catalogue to observation configuration file
If an observation catalogue file is provided, an initial configuration file can easily be generated.
More extensive information on the observation configuration file can be found on the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) page.

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

For users familiar with the MeerKAT `track.py` or `image.py` observation scripts, the input to `catalogue2obsfile.py` is similar to the standard options used by these to standard observation scripts.
```
/home/kat/katsdpscripts/observation/image.py <image.csv> --horizon=20 -t <target duration> -g <time on gain calibrator> -b <time on bandpass calibrator> -i <how often to visit bandpass calibrator> -m <total observation run time>
```

To create the associated YAML file from the standard observation options, the mapping of options between the two observation scripts are tabled below:

| `image.py` | `catalogue2obsfile.py` |
| --- | --- |
| -t | --target-duration |
| -m | --max-duration |
| -g | --secondary-cal-duration |
| -b | --primary-cal-duration |
| -i | --primary-cal-cadence |


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
python catalogue2obsfile.py --catalogue astrokat/tests/test_convert/two_calib.csv --primary-cal-duration 180

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
# AR1 mosaic NGC641
# Catalogue for the AR1 mosaic tests
3C138, radec bpcal, 05:21:09.90, +16:38:22.1
PKS 1934-638, radec bpcal fluxcal, 19:39:25.03, -63:42:45.63
PKS0153-410, radec gaincal, 01:55:37.06, -40:48:42.36
NGC641_02D02, radec target, 01:39:25.009, -42:14:49.216
NGC641_03D02, radec target, 01:37:01.491, -42:14:49.216
NGC641_02D03, radec target, 01:40:36.768, -42:37:41.000
NGC641_03D03, radec target, 01:38:13.250, -42:37:41.000
NGC641_04D03, radec target, 01:35:49.732, -42:37:41.000
NGC641_02D04, radec target, 01:39:25.009, -43:00:32.784
NGC641_03D04, radec target, 01:37:01.491, -43:00:32.784
```

For this example we assume a bandpass calibrator to be observed for 30 seconds every hour. Two calibrators are added since the targets fill the whole 24h LST range.
An example is an imaging observation with both primary and secondary calibrators, where the bandpass calibrator is only observed every 30 minutes for 3 minutes.

While the target and gain calibrator, without cadence values will be observed in sequence as listed in the catalogue. The targets for 5 minutes and the gain calibrators for 65 seconds.
```
python catalogue2obsfile.py --catalogue astrokat/tests/test_convert/image.csv --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800

# AR1 mosaic NGC641
# Catalogue for the AR1 mosaic tests
durations:
  obs_duration: 35400.0
observation_loop:
  - LST: 1.795-8.947
    target_list:
      - name=3C138, radec=05:21:09.90 +16:38:22.1, tags=bpcal, duration=180.0, cadence=1800.0
      - name=PKS 1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal fluxcal, duration=180.0, cadence=1800.0
      - name=PKS0153-410, radec=01:55:37.06 -40:48:42.36, tags=gaincal, duration=65.0
      - name=NGC641_02D02, radec=01:39:25.009 -42:14:49.216, tags=target, duration=300.0
      - name=NGC641_03D02, radec=01:37:01.491 -42:14:49.216, tags=target, duration=300.0
      - name=NGC641_02D03, radec=01:40:36.768 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_03D03, radec=01:38:13.250 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_04D03, radec=01:35:49.732 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_02D04, radec=01:39:25.009 -43:00:32.784, tags=target, duration=300.0
      - name=NGC641_03D04, radec=01:37:01.491 -43:00:32.784, tags=target, duration=300.0
```


### Adding telescope specific requirements
Most astronomy observations will require a specific observation instrument, provided by MeerKAT as correlator products. These are generally identified from the science proposal requirements, but if known upfront, can be added in the conversion step.
```
python catalogue2obsfile.py --catalogue ../tests/test_convert/image.csv --product c856M4k --band l --integration-period 8 --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800  --obsfile image.YAML

> cat  image.YAML
# AR1 mosaic NGC641
# Catalogue for the AR1 mosaic tests
instrument:
  integration_period: 8.0
  band: l
  product: c856M4k
durations:
  obs_duration: 35400.0
observation_loop:
  - LST: 1.795-8.947
    target_list:
      - name=3C138, radec=05:21:09.90 +16:38:22.1, tags=bpcal, duration=180.0, cadence=1800.0
      - name=PKS 1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal fluxcal, duration=180.0, cadence=1800.0
      - name=PKS0153-410, radec=01:55:37.06 -40:48:42.36, tags=gaincal, duration=65.0
      - name=NGC641_02D02, radec=01:39:25.009 -42:14:49.216, tags=target, duration=300.0
      - name=NGC641_03D02, radec=01:37:01.491 -42:14:49.216, tags=target, duration=300.0
      - name=NGC641_02D03, radec=01:40:36.768 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_03D03, radec=01:38:13.250 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_04D03, radec=01:35:49.732 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_02D04, radec=01:39:25.009 -43:00:32.784, tags=target, duration=300.0
      - name=NGC641_03D04, radec=01:37:01.491 -43:00:32.784, tags=target, duration=300.0
```