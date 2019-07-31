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
```
astrokat-observe.py --yaml <YAMLfile>
```
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
The OPT will assist the user to achieve an observation file, as well as observational setup, through the GUI interface.
If the OPT cannot be accessed or the astronomer selects to set up an observation offline, the following is available.
* All targets to be observed by MeerKAT requires information such as name, definition, structure and coordinate specification.
Details on the required information can be found on [Observation target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification).
* The `astrokat-targets.py` script provides an easy way of selecting [calibrators from MeerKAT observatory catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrator-selection) as well as some basic functionality for command line observation evaluation during planning.
* It is a little easier to specify the targets for new observations using CSV format [catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrators-and-CSV-catalogues).
Extracting the information from the CSV file and creating a basic YAML observation file can be achieved using the `astrokat-catalogue2obsfile.py` script described in [CSV catalogue to observation file](https://github.com/ska-sa/astrokat/wiki/Catalogues-to-observation-files).
To update a created or existing YAML observation file a standard editor can be used.
Detail information of currently supported settings are provided in [observation files](https://github.com/ska-sa/astrokat/wiki/Observation-file).   
* In addition to the default observation mode tracking a target, some scan options are also available and discussed on the [Types of target observations](https://github.com/ska-sa/astrokat/wiki/Types-of-target-observations) page.

