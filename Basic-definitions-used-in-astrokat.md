## Observation catalogue
_Minimum required information_   
List of observation targets specified as one target per line, using comma separated formatting to provide the relevant target information.   
Per target required information: **Name, Tags, RA, Decl**   
Details discussion of the per target information can be found on the [Observation target specification](https://github.com/rubyvanrooyen/astrokat/wiki/Observation-target-specification) page

Generally a catalogue is expected as part of the observation request. See [docs](https://github.com/rubyvanrooyen/astrokat/tree/master/docs) for an example observation request template

## Target catalogue to observation configuration
If a observation catalogue file is provided, an initial configuration file can easily be generated.
Instructions on how to convert a catalogue to a configuration file, as well as some examples can be found on the [Catalogues to configurations](https://github.com/rubyvanrooyen/astrokat/wiki/Catalogues-to-configurations) page


## Observation configuration file
The configuration file implements the **`YAML`** configuration format for easy parsing and usage in Python.
YAML is case sensitive and uses spaces for indentation, please no **TABS**.   
Keys and values are separated by a colon, '`:`', using 2 additional spaces indentation for key words of the next deeper layer.

Primary keys of interest to the user are: _`instrument`_, _`noise_diode`_ and **_`observation_loop`_**   
Only the _`observation_loop`_ key is required, the rest is optional and only added to the configuration file when needed.

### Instrument
The **_`instrument`_** key describes all subarray parameters required to be available in the active subarray before the observation can be executed. Currently, these include

| key | Value |
| --- | --- |
| product | Correlator product specific name |
|     | c856M4k, bc856M4k, c856M32k, bc856M32k, bc856M1k, bec856M4kssd |
| band | Observation band, currently only `l` band is available |
| dumprate | Number seconds averaging data before output |
|     | 1, 2, 4, 8 (with 8s averaging default) |
| required_antennas | Observation should not proceed if any of these antennas are not available |

The _`instrument`_ primary key is optional. If the instrument key is not specified, the observation can be executed using any active subarray. Example usage is telescope specific calibration observations that is needed for all correlator products, but generally observe the same targets.

[Optional] Instrument keys:
* _`product`_ defining the subarray correlator setup.
If the product key is not specified, the default assumption will be that the observation can be scheduled for any active instrument.
* _`band`_ defining the receptor required for observation.
If the band key is not specified, the default assumption will be that the observation can be scheduled using any receptor. Band expected: UHF, L, S and X.
* By default 8 seconds worth of data is averaged before output. The _`dumprate`_ key sets this parameter.
* _`required_antennas`_ is used if the observation can only be executed with a required antenna in the array. If this antenna becomes unavailable, the observation will not proceed.

### Noise diode

### Observation loops
An example observation contains the following elements and items:
* **Observation Loop** containing a sequence of LST ranges, each LST element provides a list of targets to observe over that sidereal time range. When converting from a catalogue, the LST range is calculated from the RA of the listed targets.
* **Target List** and **Calibration Standards** are targets, each specified as:   
_`name=<name>, radec=<HH:MM:SS.f, DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, gal=<DD:MM:SS.f, DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, azel=<az.f,el.f>, tags=<target>, duration=<sec>`_   


   
```
instrument:
  product: <name>
  dumprate: <sec>
  required_antennas: <m0XX,...>
noise_diode:
  pattern: <all> or <cycle> or <m0XX>
  on_fraction: < % >
  cycle_len: <sec>
observation_loop:
  - LST: <start>-<end>
    target_list:
      - <target>
      - ...
    calibration_standards:
      - <target>
      - ...
```


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
