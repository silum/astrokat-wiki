The observation file implemented by the astrokat observation framework uses the **`YAML`** configuration format. This is a very user friendly format that is easy to use, as well as parse in Python.

Three simple rules that the user should know:   
* YAML is **case sensitive** and uses spaces for indentation, please **no TABS**.   
* Keys and values are separated by a colon, `:`
* Use **2 additional spaces indentation** for key words of the next deeper layer.

Example configuration files with various observation types can be found on the [Observation configuration examples](https://github.com/rubyvanrooyen/astrokat/wiki/Observation-configuration-examples) page.

Primary keys of interest to the user are: _`instrument`_, _`configuration`_, _`noise_diode`_, _`durations`_ and **_`observation_loop`_**   
Only the _`observation_loop`_ key is required, the rest is optional and only added to the observation file when needed.

```
instrument:
  product: <name>
  dump_rate: <Hz>
  band: l
  pool_resources: <m0XX,...>, ptuse
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


### [Optional] Instrument
The **_`instrument`_** key describes all subarray parameters required to be available in the active subarray before the observation can be executed. Currently, these include

| key | Value |
| --- | --- |
| product | Correlator product specific name |
|     | c856M4k, bc856M4k, c856M32k, bc856M32k, bc856M1k, bec856M4kssd |
| dump_rate | Output dump rate |
|     | 1=1Hz, 2=0.5Hz, 4=0.25Hz, 8=0.125Hz (with 8s averaging default) |
| band | Observation band, currently only `l` band is available |
| pool_resources | Required antennas for observation or using PTUSE for beamformer observations |

The _`instrument`_ primary key is optional. If the instrument key is not specified, the observation can be executed using any active subarray. Example usage is telescope specific calibration observations that is needed for all correlator products, but generally observe the same targets.

[Optional] Instrument keys:
* _`product`_ defining the subarray correlator setup.
If the product key is not specified, the default assumption will be that the observation can be scheduled for any active instrument.
* _`band`_ defining the receptor required for observation.
If the band key is not specified, the default assumption will be that the observation can be scheduled using any receptor. Band expected: UHF, L, S and X.
* Observation data is averaged over some number seconds before output. This is default 8 seconds worth of data is averaged before output. The _`dump_rate`_ key sets this parameter as a rate of output, 1/#seconds [Hz]
* _`pool_resources`_ is used if the observation can only be executed with a required array setup. The observation should not proceed if any of these resources are not available.


### [Optional] Noise diode
A 20K noise diode is fitted at each receptor. The activation of the noise diode will trigger noise diodes on all antennas in the active subarray.

| key | Value |
| --- | --- |
| pattern | < all > or <m0XX, ...> |
| cycle_len | Number seconds the noise diode cycle [on/off] |
| on_fraction | < % > |

Noise diode keys:   
Although the _`noise_diode`_ key is optional, once specified, all the sub-keys must be provided to describe how the noise diode must be set.
* Noise diode _`pattern`_ can be triggered an all antennas in the array, or a list of selected antennas.
* The noise diode will cycle through an on/off pattern in the amount of time set by _`cycle_len`_, specified in seconds or fraction of seconds.
* Fraction of the cycle time the noise diode must be in the on state is provided in _`on_fraction`_ key. if the _`on_fraction`_ key is set to `-1`, the noise diode will trigger for _`cycle_len`_ before tracking a target.


### Observation loops
An observation_loop contains a sequence of LST ranges, each LST element provides a list of targets to observe over that sidereal time range. When converting from a catalogue, the LST range is calculated from the RA of the listed targets.

**Target List** and **Calibration Standards** are targets, each specified as:   
_`name=<name>, radec=<HH:MM:SS.f, DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, gal=<DD:MM:SS.f, DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, azel=<az.f,el.f>, tags=<target>, duration=<sec>`_   

Sources specified in the _`target_list`_ will be observed in the order listed in the configuration file, while sources listed in the _`calibration_standards`_ will be observed after all the targets in the _`target_list`_ has been completed, or intermittently at a user specified cadence. Targets listed in the _`target_list`_ can be observation targets of interest with accompanying gain/delay calibrators, while _`calibration_standards`_ observations are not necessarily required as part of the target cycle. A _`target_list`_ must always be provided for observation, but _`calibration_standards`_ are only provided when needed.


**Tags** are used by the telescope to classify the target type, current available options:
* Target tags indicate the type of target coordinate along with the 'target' indicator   
`radec target, azel target, gal target`
* Gain calibrator are used to solve the complex phase corrections and indicated   
`gaincal, delaycal`
* Other calibrator indicators are
 * Bandpass calibrators: `bpcal`
 * Flux calibrators: `fluxcal`
 * Polarisation calibrators: `polcal`

Optional keys provided when needed to target elements:
* For the _`target_list`_, an optional _`type`_ element can be added to indicate observation types other than tracking the target specified. Currently, an additional observation type is driftscan which can be added as _`type=driftscan`_.
* For _`calibration_standards`_ to be observed at some user specified cadence, the optional key is required: _`cadence=<sec>`_
