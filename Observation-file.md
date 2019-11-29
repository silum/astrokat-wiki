The observation file implemented by the astrokat observation framework uses the **`YAML`** configuration format. This is a very user friendly format that is easy to use, as well as parse in Python.

Three simple rules that the user should know:   
* YAML is **case sensitive** and uses spaces for indentation, please **no TABS**.   
* Keys and values are separated by a colon, '`:`'
* Use **2 additional spaces indentation** for key words of the next deeper layer.
* Comments can be added to file using the hash, '`#`', character.

A full feature example observation file is available [here](https://github.com/ska-sa/astrokat/blob/master/obs_plans/observation-template.yaml),
while more example observation files with various observation types can be found on the [Example Observation files](https://github.com/ska-sa/astrokat/wiki/Example-observation-files) page.

Primary keys of interest to the user are:   
_`instrument`_, _`horizon`_, _`noise_diode`_, _`durations`_ and **_`observation_loop`_**   
Only the _`observation_loop`_ key is required, the rest is optional and only added to the observation file when needed.

## Typical observation file
```
instrument:
  product: <name>
  integration_period: <s>
  band: l
horizon: <elev>
noise_diode:
  antennas: <all> or <cycle> or <m0XX>
  on_frac: < fractional value 0 .. 1.0 >
  cycle_len: <sec>
durations:
  obs_duration: <sec>
observation_loop:
  - LST: <start>[-<end>]
    target_list:
      - <target>
      - ...
```


### Observation loops
An _`observation_loop`_ contains a sequence of LST ranges, each LST element provides a list of targets to observe over that sidereal time range.
When [converting from a catalogue](https://github.com/ska-sa/astrokat/wiki/Catalogues-to-observation-files), the LST range is calculated from the RA of the listed targets.   
If an observation loop has multiple _`target_list`_ keys, the lists must be separated by explicitly indicating their LST range, both _`start`_ and _`end`_ as decimal angle hours.
If, however, the observation contains only a single _`target_list`_, the user need only specify a start LST, as well as the expected _`obs_duration`_ of the observation.

The **Target List** is generally specified as:   
_`name=<name>, radec=<HH:MM:SS.f DD:MM:SS.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, gal=<l.f b.f>, tags=<cal/target>, duration=<sec>`_   
_`name=<name>, azel=<az.f el.f>, tags=<target>, duration=<sec>`_   

The only key in the target definition that is not clearly self-explanatory is the _`tags`_ key.
**Tags** are used by the telescope to classify the target type:
* The explicit `target` tag is assigned to non-calibrator sources
* Calibrator tags are assigned per functionality
  * Gain calibrator to solve the complex phase corrections: `gaincal`
  * Bandpass calibrators: `bpcal`
  * Flux calibrators: `fluxcal`
  * Polarisation calibrators: `polcal`
  * Calculate delay calibration solutions: `delaycal`

Full definition of the target specification and related keys can be found on the [Observation target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification) page.


Additional keys can be added per target for specialised target specification:   
* The _`cadence=<sec>`_ key for targets such as calibrators that are to be observed at some user specified cadence.   
* The _`type=<type>`_ key can be added to indicate observation types other than tracking the target specified.   
* Target specific noise diode settings that is not suited to setting a noise diode pattern needs to be indicated using the _`nd=<off/sec>`_ key.

| key | Value |
| --- | --- |
| _`cadence`_ | Repetition time in seconds for targets that need to be visited intermittently |
| _`type`_ | Type of observation |
|     | _`track`_ (default), _`scan`_, _`raster_scan`_, _`drift_scan`_ |
| _`nd`_  | _`off`_ to disable the noise diode pattern for a target,  |
|     | or number of seconds to activate the noise diode before the target observation |

Sources specified in the _`target_list`_ will be observed in the order listed in the configuration file, with sources marked with a cadence will be inserted intermittently at a user specified cadence.
Targets listed in the _`target_list`_ can be observation targets of interest with accompanying calibrators.
A _`target_list`_ must always be provided for observation.


### [Optional] Desired observation durations and timings
All observation time related information is housed under the _`durations`_ primary key:    
The desired observation duration can be specified here using the _`obs_duration`_ sub-key and is specified in seconds.
If an observation duration is not specified, the observation sequence will be a single run through all the targets listed in order.    
Additionally, if the observation has a minimum observation time requirement to ensure science quality data, that time is indicated as _`minimum_duration`_ and specified in seconds.    
For offline planning the desired start time for the observation may also be specified.
This is one of the few planning only options that is ignored once scheduled using a system schedule block (SB). The desired start time is provided using the _`start_time`_ sub-key and assumes a '`%Y-%m-%d %H:%M:%S`' format.

| key | Value |
| --- | --- |
| _`obs_duration`_ | Desired full duration of the observation, specified in seconds |
| _`start_time`_ | For offline / local host planning the desired start time may be given |
| _`minimum_duration`_ | Shortest observation time that is required to obtain science usable data in seconds |

```
durations:
  obs_duration: <sec>
  minimum_duration: <sec>
  start_time: YYYY-mm-dd HH:MM:SS
```


### [Optional] Required instrument setup
The _`instrument`_ primary key is optional.
If the instrument key is not specified, the observation can be executed using any active subarray.

The _`instrument`_ key describes all subarray parameters **required** to be available in the active subarray before the observation can be executed.
The observation will not execute unless these resources are available in the active subarray at the time of observation.
Currently, these include

| key | Value |
| --- | --- |
| _`band`_ | Observation band defines the receptor required for observation, currently only `l` band is available |
|     | Bands expected: UHF, L, S and X |
|     | If the band key is not specified, the default assumption will be that the observation can be scheduled using any receptor.|
| _`product`_ | Correlator product specific name |
|     | c856M4k, bc856M4k, c856M32k, bc856M32k, bc856M1k |
|     | If the product key is not specified, the default assumption will be that the observation can be scheduled for any active instrument. |
| _`integration_time`_ | Observation data is averaged over some number seconds before output. This translates to the telescope parameter dump_rate with relation Hz = 1/s |
|     | 1s=1Hz, 2s=0.5Hz, 4s=0.25Hz, 8s=0.125Hz (with 8s averaging default) |
| _`pool_resources`_ | Required antennas for observation, to enable the commensal USE instruments, as well as PTUSE for beamformer observations. |
|     | The observation should not proceed if any of these resources are not available. |


### [Optional] Subarray configuration
Subarray antenna layout related to PSF calculation   
TBD


### [Optional] Noise diode
A 20K noise diode is fitted at each receptor. The activation of the noise diode will trigger noise diodes on all antennas in the active subarray.

Standard definition of the noise diode is to specify an on/off pattern that will be repeated until disabled.
The pattern is defined by specifying the optional _`noise_diode`_ primary key.
Once specified, all the secondary keys must be provided to describe how the noise diode pattern must be set.
These include the time period to repeat the pattern, _`cycle length`_, as well as the fraction of the pattern time length that the noise diode must be switched on, _`on fraction`_.
The pattern applies to the active array and can be applied to all antennas, or to a specified list of antennas.

The observation script will try and execute the command as soon as possible, with the addition of a few seconds lead time.
The noise diode _`lead_time`_ ensures that the command arrives at all the antennas before the command is executed, ensuring the all the antennas will apply the noise diode command at the same time.
The default is 3 seconds, but when desired the observer can lengthen or shorten this lead time by adding the optional _`lead_time=<sec>`_ key.

| key | Value |
| --- | --- |
| _`antennas`_ | The antennas for which the noise diode pattern should be set. Noise diode can be triggered an all antennas in the array, or a comma separated list of selected antennas. |
|    | < all > or <m0XX, ...> |
| _`cycle_len`_ | The noise diode will cycle through an on/off pattern in the amount of time set, specified in seconds or fraction of seconds. For L-band there is a maximum cycle length of 20 seconds. |
| _`on_frac`_ | Fraction of the cycle time the noise diode must be in the on state. |
| _`lead_time`_ | Lead time in seconds. Time added onto timestamps at which noise diode is set to execute the command, in order to ensure all antennas activate/deactivate the noise diode at the same time. |

When a noise diode pattern is requested it will be set at the start of the observation and deactivated at the end of the observation.
```
noise_diode:
  antennas: all
  cycle_len: 0.1  # 100ms
  on_frac: 0.5  # on/off 50% of the time
```

Refer to the [observation loop](https://github.com/ska-sa/astrokat/wiki/Observation-file#observation-loops) discussion above for specialised per target options are used when a pattern is not desired. These relate to the having a noise diode trigger before the observation of a target, _`nd=<sec>`_, or to disable the set noise diode pattern for a specific target observation, _`nd=off`_.


### [Optional] Special scan type settings
Specialised scans such as the _`scan`_ and _`raster_scan`_ options listed in the [observation loop](https://github.com/ska-sa/astrokat/wiki/Observation-file#observation-loops) tables have slightly different target definitions and require additional input information. The types of observations are discussed in detail on the [Types of target observations](https://github.com/ska-sa/astrokat/wiki/Types-of-target-observations) page.

Specific information for the _`scan`_ and _`raster_scan`_ types are:

For a single `scan` across a target, the necessary information is added using the very specific _`scan`_ primary key, followed by the sub-keys.

| key | Value |
| --- | --- |
| _`duration`_ | Minimum duration of each scan across target, in seconds |
| _`start`_ | Initial scan position as (x, y) offset in degrees |
| _`end `_ | Final scan position as (x, y) offset in degrees |
| _`projection`_ | Name of [projection](https://github.com/ska-sa/astrokat/wiki/Types-of-target-observations#specialised-scan-observation-types) in which to perform the scan|

The reader should note that target definition does not contain a **_`duration`_** option along with the _`type=scan`_ option, this is because the scan specific duration falls under the scan setup options
```
scan:
  duration: <sec>
  start: <dx,dy>
  end: <dx,dy>
  projection: plate-carree
observation_loop:
  - LST: <start>[-<end>]
    target_list:
      - name=<name>, radec=<RA> <DECl>, tags=target, type=scan
```

Alternative to a single scan is a _`raster_scan`_, which performs a series of equidistant scans across a target, either by scanning in azimuth or elevation.
If an odd number of scans are specified, the middle scan will pass directly over the target.
The default is to scan in azimuth and step in elevation, leading to a series of horizontal scans.

Similar to the scan observation type above, additional information must be provided as a _`raster_scan`_ primary key section with sub-keys, in addition to the observation target instructions.

| key | Value |
| --- | --- |
| _`num_scans`_ | Number of scans across target |
| _`scan_duration`_ | Minimum duration of each scan across target, in seconds |
| _`scan_extent`_ | Angular distance of scan along scanning coordinate, in degrees |
| _`scan_spacing`_ | Separation between each consecutive scan in degrees |
| _`scan_in_azimuth`_ | True if scanning in azimuth, while elevation remains fixed; False if scanning in elevation and stepping in azimuth instead |
| _`projection`_ | Name of [projection](https://github.com/ska-sa/astrokat/wiki/Types-of-target-observations#specialised-scan-observation-types) in which to perform the scan|


Target specification also does not have a **_`duration`_** option in the target specification, which must be provided in the _`raster_scan`_ section as a combination of _`num_scans`_ and _`scan_duration`_.
```
raster_scan:
  num_scans: <#>
  scan_duration: <sec>
  scan_extent: <deg>
  scan_spacing: <deg>
  scan_in_azimuth: True
  projection: plate-carree
observation_loop:
  - LST: <start>[-<end>]
    target_list:
      - name=<name>, radec=<RA> <DECl>, tags=target, type=raster_scan
```
