## Observation catalogue
_Minimum required information_   
List of observation targets specified as one target per line, using comma separated formatting to provide the relevant target information.
Per target required information: **Name, Tags, RA, Decl**   
Details discussion of the per target information can be found on the [Observation target specification](https://github.com/rubyvanrooyen/astrokat/wiki/Observation-target-specification) page


## Target catalogue to observation configuration
If a observation catalogue file is provided, an initial configuration file can easily be generated.
Instructions on how to convert a catalogue to a configuration file, as well as some examples can be found on the [Catalogues to configurations](https://github.com/rubyvanrooyen/astrokat/wiki/Catalogues-to-configurations) page


## Observation configuration file
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
_`name=<name>, radec=<HH:MM:SS.f, DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, gal=<DD:MM:SS.f, DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, azel=<az.f,el.f>, tags=<target>, duration=<sec>`_   

Two types of targets are specified:
* observation targets of interest with accompanying gain/delay calibrators as ordered targets
* [optional] calibration standards to be observed at some user specified cadence by adding:   
_`, cadence=<sec>`_

**Tags** are used by the telescope to classify the target type, current available options:
* Target tags indicate the type of target coordinate along with the 'target' indicator   
`radec target, azel target, gal target`
* Gain calibrator are used to solve the complex phase corrections and indicated   
`gaincal, delaycal`
* Other calibrator indicators are
 * Bandpass calibrators: `bpcal`
 * Flux calibrators: `fluxcal`
 * Polarisation calibrators: `polcal`

Sources specified in the _target list_ will be observed in the order listed in the configuration file, while sources listed in the _calibration standards_ will be observed intermittently at a user specified cadence provided in the observation instruction set.   
A _target list_ must always be provided for observation, but _calibration standards_ are only provided when needed.
