In order to achieve consistent representation and processing, MeerKAT observations are executed using the the **`astrokat`** `observe.py` script, which takes as input a human readable/editable observation configuration file. All aspects related to requirements and targets to observe is housed in this single configuration file.

**Note to the user:** MeerKAT is designed with observation scripts wrapped in schedule blocks. These schedule blocks contains most of the observational metadata related to scheduling the observation for execution, such as `desired starttime` and `observation duration`. The observation script thus is agnostic to these since control of the observation start and end is done by CAM. What is important, is that the observation script and observation configuration file, correctly represent the desired flow of the observation over the full LST range that the observation can be scheduled, to ensure that when scheduled, expected output is achieved.


## Observation planning
Observation configuration and sequence planning is mainly done by the proposing astronomer with possible assistance from a staff astronomer. This requires the generation of an observation configuration file describing a desired observation strategy that the observer builds using **`astrokat`** tools.



Observation output validated to be valid and in expected order over the whole LST range that the observation can be scheduled

## Observation verification
System verification of observation viability
Mainly done by the staff astronomer or AOD
Scheduling control is added here as part of the dry-run verification and needs to be validated against the expected full range LST output

## Observation scheduling
Observation execution on live system
This is where restrictive requirements on the _`instrument`_ will be evaluated before the observation continues
Mainly OOD responsibility