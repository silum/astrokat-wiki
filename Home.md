# Description
Observations using the **`astrokat`** `observe.py` script are controlled using an observation configuration file. This configuration file is a human readable/editable file describing a desired observation strategy that the observer builds using **`astrokat`** tools.

The configuration file implements the **`yaml`** configuration format for easy parsing and usage in Python.   
```
instrument: <name>
observation_loop:
  - LST: <start>-<end>
    target_list:
      - <target>
      - ...
    calibration_standards:
      - <target>
      - ...
```

An observation contains the following elements and items:
* **Instrument** defining the subarray correlator setup. If the instrument element is not specified, the default assumption will be that the observation can be scheduled for any active instrument.
* **Observation Loop** containing a sequence of LST ranges, each LST element provides a list of targets to observe over that sidereal time range
* **Target List** and **Calibration Standards** are targets, each specified as:   
_`name=<name>, radec=<HH:MM:SS.f,DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   

Sources specified in the _target list_ will be observed in the order listed in the configuration file, while sources listed in the _calibration standards_ will be observed intermittently at a user specified cadence provided in the observation instruction set.   
A _target list_ must always be provided for observation, but _calibration standards_ are only provided when needed.

An implementation example can be found on the [Example: Maser monitoring observations](https://github.com/rubyvanrooyen/astrokat/wiki/Example:-Maser-monitoring-observations) page
