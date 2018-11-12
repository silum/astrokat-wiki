In order to achieve consistent representation and processing, MeerKAT observations are executed using the the `astrokat-observe.py` script, which takes as input a human readable/editable observation file. All aspects related to requirements and targets to observe is housed in this single observation file.


## Creating an observation YAML file
Observation YAML files can either be created from scratch by the astronomer during observation planning, or, for an existing observation which already has a CSV catalogue and has an existing schedule block using `track.py` or `image.py` observation scripts, conversion to the YAML observation framework is quick and easy.


### Convert the CSV catalogue to a YAML observation file.
Detailed discussion of CSV catalogues to YAML observation files can be found on the [Catalogues to observation files](https://github.com/ska-sa/astrokat/wiki/Catalogues-to-observation-files) wiki page.

For this tutorial consider an example imaging observation:   
`
/home/kat/katsdpscripts/observation/image.py <image.csv> --horizon=20 -t 300 -g 65 -b 180 -i 1800 -m 35400
`

To create the associated YAML file from the standard observation options, the mapping of options between the two observation scripts are tabled below:

| `image.py` | `catalogue2obsfile.py` |
| --- | --- |
| -t | --target-duration |
| -m | --max-duration |
| -g | --secondary-cal-duration |
| -b | --primary-cal-duration |
| -i | --primary-cal-cadence |

`
python catalogue2obsfile.py --catalogue astrokat/tests/test_convert/image.csv --product c856M4k --band l --integration-period 8 --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --obsfile image.yaml
`


### Construct a YAML observation file.
A YAML observation file can be constructed using any editor using the information provided on the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) wiki page.
Alternatively, simply make a copy of an existing YAML file and edit the information appropriately.
```
> cat ../tests/test_cals/sample_targetlist_for_cals.yaml
# AR1 mosaic NGC641
# Catalogue for the AR1 mosaic tests
observation_loop:
  - LST: 1.795-8.947
    target_list:
      - name=NGC641_02D02, radec=01:39:25.009 -42:14:49.216, tags=target, duration=300.0
      - name=NGC641_03D02, radec=01:37:01.491 -42:14:49.216, tags=target, duration=300.0
      - name=NGC641_02D03, radec=01:40:36.768 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_03D03, radec=01:38:13.250 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_04D03, radec=01:35:49.732 -42:37:41.000, tags=target, duration=300.0
      - name=NGC641_02D04, radec=01:39:25.009 -43:00:32.784, tags=target, duration=300.0
      - name=NGC641_03D04, radec=01:37:01.491 -43:00:32.784, tags=target, duration=300.0
``` 


### Add standard MeerKAT calibrator to observation targets



**Note to the user:** MeerKAT is designed with observation scripts wrapped in schedule blocks. These schedule blocks contains most of the observational metadata related to scheduling the observation for execution, such as `desired starttime` and `observation duration`. The observation script thus is agnostic to these since control of the observation start and end is done by CAM. What is important, is that the observation script and observation configuration file, correctly represent the desired flow of the observation over the full LST range that the observation can be scheduled, to ensure that when scheduled, expected output is achieved.


## Observation planning
Observation configuration and sequence planning is mainly done by the proposing astronomer with possible assistance from a staff astronomer. This requires the generation of an observation configuration file describing a desired observation strategy that the observer builds using tools provided by MeerKAT.

The observation sequence is validated by the output display using the `observe.py` script with the configuration file as input. The output will cover the whole LST range that the observation can be scheduled as indicated in the configuration file.

Some example output using the tutorial configuration files:   
`python observe.py --profile ../config/drift_targets.yaml`
```
2018-09-12 08:51:44Z - Noise diode will be fired on all antenna(s) for 0.1 sec before each track or scan
2018-09-12 23:07:58Z - Found 2 target(s): 0 from 0 catalogue(s), 0 from default catalogue and 2 as target string(s)
2018-09-12 23:07:58Z - Observing targets over LST range 0.0-23.9
2018-09-12 23:07:58Z - Imaging targets are ['1934-638', '0408-65']
2018-09-12 23:07:58Z - Bandpass calibrators are []
2018-09-12 23:07:58Z - Gain calibrators are []
2018-09-12 23:07:58Z - Slewing to first target
2018-09-12 23:07:58Z - Firing noise diode for 0.1s before track on target
2018-09-12 23:07:58Z - Drift_scan target 1934-638 for 180.0 sec
2018-09-12 23:10:58Z - Firing noise diode for 0.1s before track on target
2018-09-12 23:10:58Z - Drift_scan target 0408-65 for 180.0 sec
...
...
...
2018-09-13 22:58:57Z - Drift_scan target 0408-65 for 180.0 sec
2018-09-13 23:01:57Z -

2018-09-13 23:01:57Z - Observation loop statistics
2018-09-13 23:01:57Z - Targets observed : ['1934-638' '0408-65']
2018-09-13 23:01:57Z - Time on target:
2018-09-13 23:01:57Z - 1934-638 observed for 43020.0 seconds
2018-09-13 23:01:57Z - 0408-65 observed for 43020.0 seconds
2018-09-13 23:01:57Z -

TODO: Restore defaults
2018-09-13 23:01:57Z - Switch noise-diode off
```

`python observe.py --profile ../config/image_sim.yaml`
```
2018-09-12 08:54:20Z - Switch noise-diode off
2018-09-13 10:14:22Z - Found 12 target(s): 0 from 0 catalogue(s), 0 from default catalogue and 12 as target string(s)
2018-09-13 10:14:22Z - Observing targets over LST range 11.14-23.248
2018-09-13 10:14:22Z - Imaging targets are ['T3R04C06', 'T4R00C02', 'T4R00C04', 'T4R00C06', 'T4R01C01', 'T4R01C03', 'T4R01C05', 'T4R02C02', 'T4R02C04']
2018-09-13 10:14:22Z - Bandpass calibrators are ['1934-638', '3C286']
2018-09-13 10:14:22Z - Gain calibrators are ['1827-360']
2018-09-13 10:14:22Z - Track bpcal J1939-6342 | *1934-638 for 60.0 sec
2018-09-13 10:15:21Z - Track bpcal J1331+3030 | *3C286 for 60.0 sec
2018-09-13 10:16:21Z - Track target T3R04C06 for 180.0 sec
2018-09-13 10:19:21Z - Track target T4R00C02 for 180.0 sec
2018-09-13 10:22:21Z - Track target T4R00C04 for 180.0 sec
2018-09-13 10:25:21Z - Track target T4R00C06 for 180.0 sec
2018-09-13 10:28:21Z - Track target T4R01C01 for 180.0 sec
2018-09-13 10:31:21Z - Track target T4R01C03 for 180.0 sec
2018-09-13 10:34:21Z - Track target T4R01C05 for 180.0 sec
2018-09-13 10:37:21Z - Track target T4R02C02 for 180.0 sec
2018-09-13 10:40:21Z - Track target T4R02C04 for 180.0 sec
2018-09-13 10:43:21Z - Track bpcal J1331+3030 | *3C286 for 60.0 sec
2018-09-13 10:44:21Z - Track gaincal 1827-360 for 30.0 sec
2018-09-13 10:44:51Z - Track target T3R04C06 for 180.0 sec
2018-09-13 10:47:51Z - Track bpcal J1939-6342 | *1934-638 for 60.0 sec
2018-09-13 10:48:51Z - Track target T4R00C02 for 180.0 sec
2018-09-13 10:51:51Z - Track target T4R00C04 for 180.0 sec
2018-09-13 10:54:51Z - Track target T4R00C06 for 180.0 sec
...
...
...
2018-09-13 22:30:21Z - Track gaincal 1827-360 for 30.0 sec
2018-09-13 22:30:51Z -

2018-09-13 22:30:51Z - Observation loop statistics
2018-09-13 22:30:51Z - Targets observed : ['T3R04C06' 'T4R00C02' 'T4R00C04' 'T4R00C06' 'T4R01C01' 'T4R01C03'
 'T4R01C05' 'T4R02C02' 'T4R02C04']
2018-09-13 22:30:51Z - Time on target:
2018-09-13 22:30:51Z - T3R04C06 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R00C02 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R00C04 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R00C06 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R01C01 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R01C03 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R01C05 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R02C02 observed for 4500.0 seconds
2018-09-13 22:30:51Z - T4R02C04 observed for 4500.0 seconds
2018-09-13 22:30:51Z - Calibrators observed : ['J1939-6342 | *1934-638' 'J1331+3030 | *3C286' '1827-360']
2018-09-13 22:30:51Z - Time on calibrator:
2018-09-13 22:30:51Z - J1939-6342 | *1934-638 observed for 1380.0 seconds
2018-09-13 22:30:51Z - J1331+3030 | *3C286 observed for 1560.0 seconds
2018-09-13 22:30:51Z - 1827-360 observed for 750.0 seconds
2018-09-13 22:30:51Z -

TODO: Restore defaults
2018-09-13 22:30:51Z - Switch noise-diode off
```

`python observe.py --profile ../config/image.yaml`   
`python observe.py --profile ../config/OH_periodic_masers.yaml`


## Observation verification
System verification of observation viability
Mainly done by the staff astronomer or AOD
Scheduling control is added here as part of the dry-run verification and needs to be validated against the expected full range LST output

## Observation scheduling
Observation execution on live system
This is where restrictive requirements on the _`instrument`_ will be evaluated before the observation continues
Mainly OOD responsibility