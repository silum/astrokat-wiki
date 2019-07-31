Some observations may include the use of a noise diode. The current understanding of the various noise diode implementation requirements are described here.

Noise diode implementation for the observation framework provides the following functionality:   
_`on`_, _`off`_, _`trigger`_ and _`pattern`_

While switching the noise diode _`on`_ and _`off`_ is a straight forward implementation, the _`trigger`_ and _`pattern`_ settings needs to be clarified a little more.

Standard definition of the noise diode is to specify an on/off _`pattern`_ that will be repeated until disabled. The pattern is defined by specifying the time period to repeat the pattern, `cycle length`, in seconds, as well as the fraction of the pattern time length that the noise diode must be switched on, `on fraction`. This can be applied to all antennas, or to a specified list of antennas.

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
More information on usage options for setting the noise diode pattern can be found in that section of the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) page.

**Note to the user**:
When a noise diode pattern is requested it will be set at the start of the observation and deactivated at the end of the observation.

The ability to _`trigger`_ the noise diode between tracks for a specified number of seconds is implemented as a per target option, eg _`nd=10`_ to trigger the noise diode for 10 seconds before a track of the target.
See target discussion in the **Observation loops** section of the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) page.

The per target implementation of the noise diode trigger, provides the additional ability to deactivate a noise diode pattern for a selected target observation, _`nd=off`_, and then to reactivate the pattern for the remainder of the observation.

**Implementation note**:   
It takes time for the command to trigger the noise diode to get to all the digitisers, so in order to ensure the on/off setting of the noise diodes are in sync, a timestamp at which to execute the command must be provided. Else the on/off event will the staggered depending on when the command reaches the digitiser and get executed.

To ensure all noise diode events always happen in sync, a default lead time of five, 5.0, seconds are added to the time at which the command is requested. Thus ensuring the command are received by all digitisers and will trigger simultaneously.