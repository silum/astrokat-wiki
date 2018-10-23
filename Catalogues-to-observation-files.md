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

The `catalogue2config.py` script does simple conversion of existing observation catalogues, CSV format, to configuration files, YAML format.
```
catalogue2config.py -h
```
Required input parameters are the name of the catalogue file, `--catalogue`, as well as an on target duration in seconds, `--target-duration`.   
For convenience the output will be displayed to screen if an output filename, `--obsfile`, is not specified.  
Once the user is satisfied with the output, an observation profile can be created   
```
python catalogue2config.py --catalogue targets.csv --obsfile targets.yaml ...
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
python catalogue2config.py --catalogue targets.csv --target-duration 10
```
Resulting in a basic target list
```
instrument:
observation_loop:
  - LST: 0.000-23.9
    target_list:
      - name=target0_radec, radec=0 -90, tags=target, duration=10.0
      - name=target1_azel, azel=10 50, tags=target, duration=10.0
      - name=target2_gal, gal=-10 40, tags=target, duration=10.0
```
An empty key for _`instrument`_ indicate that currently only the target sequence is important and will be evaluated.   
No _`LST`_ range was specified in the conversion, so the range over which the targets are visible has been inserted by default.   
Also, target names were not provided, resulting in generated names to be created.   
Each target has its own _`tags`_ and _`duration`_. These can be updated by the user.

### Changing the type of observation
The default observation type is to track the target for the duration specified.
The type key can also be used to specify alternative observation types, currently only driftscans are implemented.
```
python catalogue2config.py --catalogue targets.csv --target-duration 10 --drift-scan
```
Resulting on a _`type`_ key being added indicating that the targets must be observed using a drift scan
```
instrument:
observation_loop:
  - LST: 0.000-23.9
    target_list:
      - name=target0_radec, radec=0 -90, tags=target, duration=10.0, type=drift_scan
      - name=target1_azel, azel=10 50, tags=target, duration=10.0, type=drift_scan
      - name=target2_gal, gal=-10 40, tags=target, duration=10.0, type=drift_scan
```
Optional is to remove the key for those targets not requiring a drift scan, or explicitly adding the track observation type.
```
instrument:
observation_loop:
  - LST: 0.000-23.9
    target_list:
      - name=target0_radec, radec=0 -90, tags=target, duration=10.0, type=track
      - name=target1_azel, azel=10 50, tags=target, duration=10.0, type=track
      - name=target2_gal, gal=-10 40, tags=target, duration=10.0, type=drift_scan
```


### Adding observation calibrators
Gain calibrators is generally intermingled with the source target list, but primary calibrators such and bandpass and polarisation calibrators need only be visited at some slow cadence.
```
, radec target, 0, -90
, azel target, 10, 50
, gal target, -10, 40
1934-638,radec bpcal,19:39:25.03,-63:42:45.63
0408-65,radec bpcal,04:08:20.38,-65:45:09.1
```
When calibrators are present in the catalogue, they are added to the _`calibration_standards`_ key of the _`observation_loop`_. This makes the initial presentation clean and easy to edit by the user should there be a need to move the calibrator to the target list to ensure the calibrator is observation in sequence.
For this example we assume a bandpass calibrator to be observed for 30 seconds every hour. Two calibrators are added since the targets fill the whole 24h LST range.
```
python catalogue2config.py --catalogue targets.csv --target-duration 10 --bpcal-duration 30 --bpcal-interval 3600
```
Updating the observation configuration to
```
instrument:
observation_loop:
  - LST: 0.000-23.9
    target_list:
      - name=target0_radec, radec=0 -90, tags=target, duration=10.0
      - name=target1_azel, azel=10 50, tags=target, duration=10.0
      - name=target2_gal, gal=-10 40, tags=target, duration=10.0
    calibration_standards:
      - name=1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal, duration=30.0, cadence=3600.0
      - name=0408-65, radec=04:08:20.38 -65:45:09.1, tags=bpcal, duration=30.0, cadence=3600.0
```


### Adding telescope specific requirements
Most astronomy observations will require a specific observation mode, provided by MeerKAT as correlator products. These are generally identified from the science proposal requirements, but if known upfront, can be added in the conversion step.
```
python catalogue2config.py --catalogue targets.csv --target-duration 10 --bpcal-duration 30 --bpcal-interval 3600 --product bc856M4k  --dump-rate 2
```
Adding the additional information to the _`instrument`_ key, setting the observation mode to beamformer/correlator wide channel mode, and averaging data for 2 seconds.
```
instrument:
  product: bc856M4k
  dump_rate: 0.5
observation_loop:
  - LST: 0.000-23.9
    target_list:
      - name=target0_radec, radec=0 -90, tags=target, duration=10.0
      - name=target1_azel, azel=10 50, tags=target, duration=10.0
      - name=target2_gal, gal=-10 40, tags=target, duration=10.0
    calibration_standards:
      - name=1934-638, radec=19:39:25.03 -63:42:45.63, tags=bpcal, duration=30.0, cadence=3600.0
      - name=0408-65, radec=04:08:20.38 -65:45:09.1, tags=bpcal, duration=30.0, cadence=3600.0
```

