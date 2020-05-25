Some observations may include the use of a noise diode. The current understanding of the various noise diode implementation requirements are described here.

Noise diode implementation for the observation framework provides the following functionality:   
_`on`_, _`off`_, _`trigger`_ and _`pattern`_
* The _`on`_, _`off`_ and _`trigger`_ functionality is specified per target and the noise diodes on all antennas in the array is activated/deactivated with these commands.    
* The _`pattern`_ functionality is currently the most expected usage and more mature implementation. This functionality includes the ability for the user to only use selected antennas when setting the noise diode activation pattern for an observation.

While switching the noise diode _`on`_ and _`off`_ is a straight forward implementation, the _`pattern`_ and _`trigger`_ settings needs to be clarified a little more.

**Setting a noise diode activation pattern for the observation**:   
The standard usage of the noise diode is to specify an on/off _`pattern`_ that will be repeated until disabled. The _`pattern`_ is defined by specifying the time period to repeat the pattern, `cycle length`, in seconds, as well as the fraction of the pattern time length that the noise diode must be switched on, `on fraction`.
This _`pattern`_ can be applied to `all` antennas (`antennas: all`), or to a comma separated list of antennas, e.g. `antennas: m011,m022,...`

For example setting a noise diode pattern that will repeat every 100 ms with an on/off fraction of 50% of the cycle time:
```
noise_diode:
  # set noise diode pattern on all antennas associated to the active subarray
  antennas: all
  # set an on/off noise diode cycle of 100 milliseconds
  cycle_len: 0.1  # sec
  # use a 50% duty cycle to switch the noise diode on
  on_frac: 0.5  # fractional value 0 .. 1.0
```

In select cases the ability to deactivate the noise diode pattern for a specific target observation is desirable.
The per target implementation of the noise diode, provides this additional ability by adding the _`nd=off`_ option to the target in the observation file.
The noise diode pattern will be disabled when observing this target and continued for the other observation targets.
```
# Set noise diode pattern, but switch off for target observation
noise_diode:
  antennas: all
  cycle_len: 0.1  # 100ms
  on_frac: 0.5  # 50%
observation_loop:
  - LST: 0:00
    target_list:
      # observe target with noise diode patter
      - name=azel, azel=50.26731 43.70517, tags=target, duration=60.0
      # disable the noise diode pattern for this target
      - name=azel, azel=50.26731 43.70517, tags=target, duration=120.0, nd=off
      # observe target with noise diode patter
      - name=azel, azel=50.26731 43.70517, tags=target, duration=60.0
```


More information on usage options for setting the noise diode pattern can be found in that section of the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) page.

**Note to the user**:
When a noise diode pattern is requested it will be set at the start of the observation and deactivated at the end of the observation.

**Per target noise diode _`trigger`_**:    
The ability to _`trigger`_ the noise diode between tracks for a specified number of seconds is implemented as a per target option, e.g. _`nd=10`_ to trigger the noise diode for 10 seconds before a track of the target.   
This per target option (as with noise diode _`on`_/_`off`_) will be activated on all antennas in the array.
```
noise_diode:
  # set lead time for trigger command
  lead_time: 5.  # sec
observation_loop:
  - LST: 0:00
    target_list:
      # trigger noise diode before track for 15 sec as with pattern
      - name=azel, azel=50.26731 43.70517, tags=target, duration=300.0, nd=10
```
It takes time for the command to trigger the noise diode to get to all the digitisers, so in order to ensure the on/off setting of the noise diodes are in sync, a timestamp at which to execute the command must be provided. Else the on/off event will the staggered depending on when the command reaches the digitiser and get executed.     
To ensure all the antenna noise diodes in the array are activated and deactivated synchronously, the system will apply a default `3 second` time delay (`lead_time`) before executing the command.
This default `lead_time` can be adjusted by the observer by adding the `noise_diode` option in the example above
```
noise_diode:
  # set lead time for trigger command
  lead_time: 5.  # sec
```
Also see the per target usage instructions in the **Observation loops** section of the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) page.
