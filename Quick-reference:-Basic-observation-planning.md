## Creating an observation file
1. Starting off with only a target and coordinates -- create an observation CSV catalogue
```
astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain flux --cat-path astrokat/catalogues/ --outfile 'astrokat_catalogue.csv' --lst --datetime '2019-2-6 14:52:48' --horizon 20
```
2. List rise and set times in LST and update listed targets accordingly
```
astrokat-cals.py --view astrokat_catalogue.csv --lst --horizon 20
```
3. Convert CSV catalogue to YAML observation file
```
astrokat-catalogue2obsfile.py --infile astrokat_catalogue.csv --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --outfile astrokat_obsfile.yaml
```
4. Update the `.yaml` file to correct LST range, observation period or source observation sequences, as needed.
```
astrokat-cals.py --view astrokat_obsfile.yaml --lst --text-only --horizon 20
```


## Adding or removing observation targets
**It is strongly advised not to update an existing YAML file manually.**

Add or remove targets and calibrators from an existing observation should only be done by editing the observation CSV catalogue.   
If this catalogue does not exist, regenerate a CSV catalogue using the steps described above.

For an existing CSV catalogue, the following steps can be used to obtain an update observation YAML file.

1. Find calibrators for new target, but this time do not create a CSV file, only display the output to screen
```
astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain flux --cat-path astrokat/catalogues/ --lst --horizon 20
```

2. Add target and calibrator information to the existing observation CSV catalogue

3. Convert the updated CSV catalogue to YAML observation file
```
astrokat-catalogue2obsfile.py --infile astrokat_catalogue.csv --target-duration 300 --max-duration 35400  --secondary-cal-duration 65 --primary-cal-duration 180 --primary-cal-cadence 1800 --outfile astrokat_obsfile.yaml
```

4. Display and verify update information   
When new targets are added, or redundant targets are removed, it is most important to verify that the LST range specified are still relevant since this will be used to evaluate the viability of the observation.
```
astrokat-cals.py --view astrokat_obsfile.yaml --lst --horizon 20
```

## Observation planning and timing refinements

Rise and set times for all sources in an observation file is related to the LST range over which the sources are visible. An observation using the YAML framework can be scheduled anywhere within this range.

For observation verification and simulation, the source visibility in UTC must be displayed and evaluated.
It can simply be displayed at the current time, or it can be displayed starting from the desired UTC schedule time using the `--datetime` option.

1. Find appropriate UTC time to simulate or schedule the observation   
Always validate LST values displayed at on the bottom x-axis are within the LST range listed in the observation file, and select the best rise and set starting UCT time to simulate the observation.
```
astrokat-cals.py --view astrokat_obsfile.yaml --horizon 20
astrokat-cals.py --view astrokat_obsfile.yaml --horizon 20 --datetime '2019-2-6 10:15:56'
```

2. Edit the observation script to add this selected start time
```
durations:
   start_time: 2019-02-06 10:15:56
```

3. Run the observation and carefully read and evaluate the output sequence and timing information
```
astrokat-observe.py --yaml astrokat_obsfile.yaml
```