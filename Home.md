# Introduction
For MeerKAT the process of getting an observation from planning to observation execution can be grouped into two components.
* Planning prior to observation
* Verification and execution

The `AstroKAT` framework is a package to assist the astronomer with observation planning both during the proposal phase, as well as when setting up for actual observations.
It consists of python scripts that compliment the MeerKAT Observation Planning Tool (OPT) and provide simple tools for users to set up observations for submission via the OPT.

The main function of `AstroKAT` is the standard observation script.
The `astrokat-observe.py` script is the MeerKAT observation script that interprets and execute the observation as specified in the input YAML file.
In addition, the same script will simulate the expected observation sequence when executed independent of the MeerKAT systems.
Thus, allowing the astronomer to get an idea of observation timings and target sequence independent of the MeerKAT telescope interface.

The input parameter to `AstroKAT` is an observation file that contains the observation targets and associated instructions, such as the integration period and cadence.
It is important to note that the generated observation file is a YAML file that is a list of targets, with per target instructions, that describes the desired observation sequence.
The YAML file is independent of the observation script that will interpret and execute the observation, as well as the MeerKAT telescope operations interface that will schedule and run the observation.

Even though the output of the observation script executing the observation file will look similar, the three steps of execution have very different goals.
1. Initially running the observation script independent to the MeerKAT telescope, will generate an anticipated observation sequence for validation by the astronomer.
This is independent of the actual MeerKAT system and uses timing simulations to generate the output.   
The astronomer evaluates this output to verify the order in which the targets are observed are acceptable, as well as the time spend on each target to ensure maximum use of time allocated.

2. Before observation, the MeerKAT operational system will use the observation script to perform a dry-run to verify the validity of the observation to be executed on the telescope given current time and instrument setup.   
The Astronomer on Duty (AOD) and Operator on Duty (OOD) use this output to evaluate the success of the observation, as well as verify if the observation will proceed as requested.
The output of this can be provided to the proposing astronomer as well for planning and target sequence verification.

3. If the observation dry-run is successful, the MeerKAT operational system will execute the observation.
This is the only stage at which the telescope will actually execute the observation instructions in the observation file, as interpreted by the observation script.


## Anticipated observation build sequence
The general sequence from proposal to observation execution are as follows:
1. The proposing / observing astronomer generates the observation YAML file (either independent or through the MeerKAT OPT)
2. A schedule block is created by the AOD for each observation
3. For each observations, the AOD will also perform a dry-run to verify the observation setup and target sequence.
The AOD may use the dry-run to communicate with the astronomer to refine and finalise the observation.
4. Once the observation is accepted and scheduled, the OOD ensures every observation is verified before observation using the dry-run, as well as queue the observation for execution.


## Targets and observation specification

* All targets to be observed by MeerKAT requires information such as name, definition, structure and coordinate specification.
The OPT will assist the user through the GUI interface to specify or selects targets, alternatively information on the required information can be found on [Observation target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification).

* Additional scripts provide an easy way of selecting [calibrators from MeerKAT observatory catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrator-selection) as well as some basic functionality for command line observation evaluation during planning.

* It is a little easier to specify the targets for new observations using CSV format [catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrators-and-CSV-catalogues).
Extracting the information from the CSV file and creating a basic YAML observation file can be achieved using the `astrokat-catalogue2obsfile.py` script described in [CSV catalogue to observation file](https://github.com/ska-sa/astrokat/wiki/Catalogues-to-observation-files).
To update a created or existing YAML observation file a standard editor can be used.
Detail information of currently supported settings are provided in [observation files](https://github.com/ska-sa/astrokat/wiki/Observation-file).   

* In addition to the standard observation of tracking a target, some scan options are also available and discussed on the [Types of target observations](https://github.com/ska-sa/astrokat/wiki/Types-of-target-observations) page.

## Noise diode usage during observations
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

To ensure all noise diode events always happen in sync, a default lead time of two, 2.0, seconds are added to the time at which the command is requested. Thus ensuring the command are received by all digitisers and will trigger simultaneously.

## Verification needs
The observation framework provides a number of usage levels and currently requires three, 3, stages where verification must occur during development.
* Most development should take place on a local framework setup and thus using the `offline` implementation. This part is focus solely on getting the anticipated observational behaviour. It is agnostic to the system specifications and subarray setup, which is only verified in the following verification steps.
* Verifying the subarray and setup related observational requirements are done during the CAM `dry-run` simulation step. This step requires access to one of the CAM development systems, with the default VM being [devcomm](http://monctl.devcomm.camlab.kat.ac.za/katgui/home).
* The last verification happens as part of the scheduling of the observation on the `live` system. This is where the required resources and antenna setup specifications are evaluated before observation.


# Correlator observational requirements
This section focus on the information and options required for interferometry observations such as imaging and spectral line observations. Focus on the observational needs during planning and execution, and how to represent these requirements in the YAML file

A number of [observation types](https://github.com/ska-sa/astrokat/wiki/Types-of-target-observations) are available with the standard tracking of a target the default observation type.


## Migrating imaging observations to the new framework:

### Setting up the observation framework   
Getting a local copy: `git clone git@github.com:ska-sa/astrokat.git`   
Using the sandbox system: `ssh -Y kat@calvin.sdp.kat.ac.za`   
Using devcomm: `ssh kat@monctl.devcomm.camlab.kat.ac.za`

### [Optional] Easy generation of observation CSV catalogue with calibrators   
This step is only required if the observation target list does not include calibrator sources and good calibrators have to be added.
This is not a required step, since a CSV catalogue or observation YAML file can be created directly.
The aim of the [MeerKAT calibrator selection](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrator-selection) tool is to simply the selection of good calibrators.

Usage on a local copy or the sandbox (`cd framework/astrokat/scripts`)
```
python astrokat-cals.py --prop-id 'SCI-propid-0' --pi 'No One' --contact 'dummy@ska.ac.za' --cal-tags gain bp pol flux delay --outfile ../output/catalogue_name.csv --infile ../input/sample_targetlist_for_cals.csv --datetime '2018-08-06 12:34' --cat-path ../../katconfig/user/catalogues/
```

Usage on the devcomm or live systems (`cd usersnfs/framework/astrokat/scripts`)
```
python astrokat-cals.py --prop-id 'SCI-prop-id' --pi 'No One' --contact 'dummy@ska.ac.za' --cal-tags gain bp pol flux delay --outfile /home/kat/usersnfs/framework/extras/catalogue_name.csv --infile /home/kat/usersnfs/framework/extras/sample_targetlist_for_cals.csv --datetime '2018-08-06 12:34'
```

Note that the live systems does not have processing packages installed, thus only text output to screen will be displayed and the catalogue created
```
Required matplotlib functionalities not available
Cannot create elevation plot or generate report
Only producing catalogue file and output to screen
```
If the user prefers having the output display, then the scripts on the sand box should be used.


### [Optional] Easy conversion of CSV catalogues to YAML observation files
The observation framework uses target specific information that is provided in the observation YAML file as detailed on the [Observation file](https://github.com/ska-sa/astrokat/wiki/Observation-file) wiki page.   

These format was selected to be easy to use by the astronomer and can be created manually. For existing observations that needs to be re-observed, getting an initial YAML file is made easy with a quick conversion tool, _`catalogue2obsfile.py`_.

The script arguments are verbose to ensure ease of use, with duration and cadence settings similar to the _`image.py`_ and _`track.py`_ scripts.
```
python catalogue2obsfile.py --catalogue /home/kat/usersnfs/framework/extras/catalogue_name.csv --target-duration 300 --primary-cal-duration 180 --primary-cal-cadence 1800 --secondary-cal-duration 65 --obsfile /home/kat/usersnfs/framework/extras/catalogue_name.yaml
```




# Beamformer specific observational requirements
Information and options required during observation planning and execution in the YAML file
* Display beam shape from antennas selected and UV coverage for integration time
* Beamformer observation input

Functionality represented in tag values -- why and which observations needs them
* Beamformer observation functionality

# Using the observation framework (a simple HowTo)
### Using the sandbox to plan an observation

