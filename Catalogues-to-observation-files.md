## Observation catalogue
The _minimum required information_ for any observation is a list of observation targets specified as one target per line, using comma separated formatting to provide the relevant target information.   
Per target required information:   
`[name], tags, ra, dec` or `[name], tags, az, el`   
Details discussion of the per target information can be found on the [Observation target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification) page

The targets can be celestial or horizontal.    
Specials such as TLE for satellites and near earth objects are not currently available.    
Generally a catalogue is expected as part of the observation request.


## Converting target catalogue to observation configuration file
If an observation catalogue file is provided, an initial configuration file can easily be generated.
More extensive information on the observation configuration file can be found on the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) page.

The `astrokat-catalogue2obsfile.py` script does simple conversion of existing observation catalogues, CSV format, to configuration files, YAML format.
```
astrokat-catalogue2obsfile.py -h

usage: /Users/ruby/Projects/gitSDP/astrokat/venv/bin/astrokat-catalogue2obsfile.py [options] --infile <full_path/cat_file.csv>
...

```

The only required input parameter is the name of the catalogue file, `--infile`.   
For convenience the YAML output will be displayed to screen if an output filename, `--outfile`, is not specified.  
Once the user is satisfied with the output, an observation profile can be created   
`astrokat-catalogue2obsfile.py --infile targets.csv --outfile targets.yaml ...`

For users familiar with the MeerKAT `track.py` or `image.py` observation scripts, the input to `astrokat-catalogue2obsfile.py` is similar to the standard options used by these to standard observation scripts.
```
/home/kat/katsdpscripts/observation/image.py <image.csv> --horizon=20 -t <target duration> -g <time on gain calibrator> -b <time on bandpass calibrator> -i <how often to visit bandpass calibrator> -m <total observation run time>
```

To create the associated YAML file from the standard observation options, the mapping of options between the two observation scripts are tabled below:

| `image.py` | `astrokat-catalogue2obsfile.py` |
| --- | --- |
| -t | --target-duration |
| -m | --max-duration |
| -g | --secondary-cal-duration |
| -b | --primary-cal-duration |
| -i | --primary-cal-cadence |


### Adding observation source targets
Basic steps for conversion can be illustrated using some random targets, `targets.csv`
```
, radec target, 0:00:00, -90:00:00
, azel target, 10, 50
, gal target, -10, 40
Moon, special
```

Simple convert targets to configuration
```
astrokat-catalogue2obsfile.py --infile targets.csv --target-duration 10
```
Resulting in a basic target list
```
horizon: 20.0
observation_loop:
  - LST: 0:00-23:59
    target_list:
      - name=target0_radec, radec=0 -90, tags=target, duration=10.0
      - name=target1_azel, azel=10 50, tags=target, duration=10.0
      - name=target2_gal, gal=-10 40, tags=target, duration=10.0
      - name=Moon, special=special, tags=target, duration=10.0
```
No _`LST`_ range was specified in the conversion, so a start LST for the targets have been inserted by default.   
Target names were also not provided, resulting in generated names to be added.   
Each target has its own _`tags`_ and _`duration`_, which can be updated by the user.

The source list construction does not distinguish between targets and calibrators when converting from CSV format catalogues to observation files.
When converting catalogues that contains calibrators, the user must specify the associated timings required.
```
astrokat-catalogue2obsfile.py --infile two_calib.csv --primary-cal-duration 180

horizon: 20
observation_loop:
  - LST: 12:30
    target_list:
      - name=1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal, duration=180.0
      - name=0408-65, radec=04:08:20.38 -65:45:09.1, tags=bpcal, duration=180.0
```
In general the default application assumes _`gaincal`_ as secondary calibrators, while _`bpcal`_, _`fluxcal`_ and _`polcal`_ targets are considered primary calibrators.


### Observations with targets and calibrators
Gain calibrators are generally intermingled with the source target list, with primary calibrators often need only be visited at some slower cadence.
```
# AR1 mosaic NGC641
# Catalogue needed for the AR1 mosaic tests
J0408-6545 | 0408-658, radec bpcal fluxcal, 4:08:20.38, -65:45:09.6, (145.0 18000.0 -0.979 3.366 -1.122 0.0861)
J1939-6342 | 1934-638, radec bpcal fluxcal, 19:39:25.05, -63:42:43.6, (408.0 8640.0 -30.77 26.49 -7.098 0.6053)
J0155-4048 | 0153-410, radec gaincal, 1:55:37.06, -40:48:42.4
NGC641_02D02, radec target, 1:39:25.01, -42:14:49.2
NGC641_02D03, radec target, 1:40:36.77, -42:37:41.0
NGC641_02D04, radec target, 1:39:25.01, -43:00:32.8
NGC641_03D02, radec target, 1:37:01.49, -42:14:49.2
NGC641_03D03, radec target, 1:38:13.25, -42:37:41.0
NGC641_03D04, radec target, 1:37:01.49, -43:00:32.8
NGC641_04D03, radec target, 1:35:49.73, -42:37:41.0
```

Example of an imaging observation with both primary and secondary calibrators, where the bandpass calibrator is only observed every 30 minutes for 3 minutes.
The targets for 5 minutes and the gain calibrators for 65 seconds.
```
astrokat-catalogue2obsfile.py --infile AR1_mosaic_NGC641.csv --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --outfile AR1_mosaic_NGC641.yaml

# Catalogue needed for the AR1 mosaic tests
# AR1 mosaic NGC641
# Observation catalogue for proposal ID None
# PI: None
# Contact details: None
horizon: 20
durations:
  obs_duration: 35400
observation_loop:
  - LST: 18:12
    target_list:
      - name=J0010-4153 | 0008-421, radec=0:10:52.52 -41:53:10.8, tags=bpcal, duration=180.0, cadence=1800.0, model=(145.0 20000.0 -16.93 15.39 -4.21 0.3496)
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=NGC641_02D02, radec=1:39:25.01 -42:14:49.2, tags=target, duration=300.0
      - name=NGC641_02D03, radec=1:40:36.77 -42:37:41.0, tags=target, duration=300.0
      - name=NGC641_02D04, radec=1:39:25.01 -43:00:32.8, tags=target, duration=300.0
      - name=NGC641_03D02, radec=1:37:01.49 -42:14:49.2, tags=target, duration=300.0
      - name=NGC641_03D03, radec=1:38:13.25 -42:37:41.0, tags=target, duration=300.0
      - name=NGC641_03D04, radec=1:37:01.49 -43:00:32.8, tags=target, duration=300.0
      - name=NGC641_04D03, radec=1:35:49.73 -42:37:41.0, tags=target, duration=300.0
```

The target and gain calibrator, without cadence values will be observed in sequence as listed in the catalogue.
Thus, the YAML file must be edited to insert the gain calibrator in the observation sequence where needed.
```
# AR1 mosaic NGC641
# Catalogue for the AR1 mosaic tests
durations:
  obs_duration: 35400.0
observation_loop:
  - LST: 12:30-11:30
    target_list:
      - name=J0408-6545 | 0408-658, radec=4:08:20.38 -65:45:09.6, tags=bpcal fluxcal, duration=180.0, cadence=1800.0, model=(145.0 18000.0 -0.979 3.366 -1.122 0.0861)
      - name=J1939-6342 | 1934-638, radec=19:39:25.05 -63:42:43.6, tags=bpcal fluxcal, duration=180.0, cadence=1800.0, model=(408.0 8640.0 -30.77 26.49 -7.098 0.6053)
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=NGC641_02D02, radec=1:39:25.01 -42:14:49.2, tags=target, duration=300.0
      - name=NGC641_02D03, radec=1:40:36.77 -42:37:41.0, tags=target, duration=300.0
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=NGC641_02D04, radec=1:39:25.01 -43:00:32.8, tags=target, duration=300.0
      - name=NGC641_03D02, radec=1:37:01.49 -42:14:49.2, tags=target, duration=300.0
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
      - name=NGC641_03D03, radec=1:38:13.25 -42:37:41.0, tags=target, duration=300.0
      - name=NGC641_03D04, radec=1:37:01.49 -43:00:32.8, tags=target, duration=300.0
      - name=NGC641_04D03, radec=1:35:49.73 -42:37:41.0, tags=target, duration=300.0
      - name=J0155-4048 | 0153-410, radec=1:55:37.06 -40:48:42.4, tags=gaincal, duration=65.0
```

### Adding telescope specific requirements
Most astronomy observations will require a specific observation instrument, provided by MeerKAT as correlator products. These are generally identified from the science proposal requirements, but if known upfront, can be added in the conversion step.
```
astrokat-catalogue2obsfile.py --infile sample_targetlist_for_cals.csv --product c856M4k --band l --integration-period 8 --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --outfile sample_targetlist_for_cals.yaml
```
Which will add the following additional options to the YAML observation file
```
# Catalogue needed for the AR1 mosaic tests
# AR1 mosaic NGC641
instrument:
  enabled: False
  product: c856M4k
  integration_period: 8.0
  band: l
  pool_resources: cbf,sdp
horizon: 20
durations:
  obs_duration: 35400
observation_loop:
  - LST: 19:35
    target_list:
...
```

