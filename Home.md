# Introduction
For MeerKAT the process of getting an observation from planning to observation execution can be grouped into two main components.
* Planning prior to observation
* Verification and execution

The `AstroKAT` framework is a package to assist the astronomer with observation planning starting at the proposal phase, up to setting up for actual observations.
It consists of python scripts that form the backend to the MeerKAT Observation Planning Tool (OPT), as well as provide simple command line tools for users to set up observations for submission via the OPT.

The input parameters to `AstroKAT` is specified using an observation file that contains the observation targets and associated instructions, such as the integration period and cadence.
It is important to note that the generated observation file is a YAML file that contains a list of targets, with per-target instructions, thus describing the desired observation sequence.
The YAML file is independent of the observation script that will interpret and execute the observation, as well as the MeerKAT telescope operations interface that will schedule and run the observation.

The main function of `AstroKAT` is the standard observation script.
The `astrokat-observe.py` script is the MeerKAT observation script that interprets and executes the observation as specified in the input YAML observation file.
In addition, the same script will simulate the expected observation timing and associated target sequence when executed independent of the MeerKAT systems,
thus, allowing the astronomer to get an idea of a good observation strategy independent of the MeerKAT telescope interface.
```
astrokat-observe.py --yaml <YAMLfile>
```

Even though the output of the observation script executing the observation file will look similar, the two components making up observation planning and execution have very different goals.
Ultimately, the first component is planning only through simulation, while the second component adds two steps of validation to ensure good quality science data and successful operational setup.

In essence three steps will be required to move from planning to observation:

1. Planning simulations of the observation using the observation script independent to the MeerKAT telescope.
The output of this step is an anticipated observation sequence for validation by the astronomer.
Since this step is independent of the actual MeerKAT system, it uses timing simulations to generate the output.   
The astronomer evaluates this output to verify whether the order in which the targets are observed are acceptable, as well as to determine the time spent on each target to ensure maximum use of time allocated.

2. After submitting the planned observation to MeerKAT, the MeerKAT operational system will use the observation script to perform a dry-run to verify the validity of the observation to be executed on the telescope given current time and instrument setup.   
The Astronomer on Duty (AOD) and Operator on Duty (OOD) uses this output to evaluate the success of the observation, as well as verify if the observation will proceed as requested.
The output of this can be provided to the proposing astronomer as well for planning and target sequence verification.

3. If the observation dry-run is successful, the MeerKAT operational system will execute the observation.
This is the only stage at which the telescope will actually execute the observation instructions in the observation file, as interpreted by the observation script.


## Anticipated observation build sequence
The general sequence from proposal to observation execution are as follows:
1. The proposing / observing astronomer generates the observation YAML/JSON file (either independent or through the MeerKAT OPT)
2. A schedule block is created by the astronomer/AOD for each observation
3. For each observation, the AOD will also perform a dry-run to verify the observation setup and target sequence.
The AOD may use the dry-run to communicate with the astronomer to refine and finalise the observation.
4. Once the observation is accepted and scheduled, the OOD ensures every observation is verified before observation using the dry-run, as well as queue the observation for execution.


## Targets and observation specification
The OPT will assist the user to achieve a MeerKAT schedule block with instructions in an observation file, as well as observational setup, through the GUI interface.

If the OPT cannot be accessed or the astronomer elects to set up an observation offline, the following is available.
* All targets to be observed by MeerKAT requires information such as name, definition, structure and coordinate specification.
Details on the required information can be found on [Observation target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification).
* The `astrokat-targets.py` script provides a utility function for selecting [calibrators from MeerKAT observatory catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrator-selection) as well as some basic functionality for command line observation evaluation during planning.
* It is a little easier to specify the targets for new observations using CSV format [catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrators-and-CSV-catalogues).
Extracting the information from the CSV file and creating a basic YAML observation file can be achieved using the `astrokat-catalogue2obsfile.py` script described in [CSV catalogue to observation file](https://github.com/ska-sa/astrokat/wiki/Catalogues-to-observation-files).
To update a created or existing YAML observation file a standard text editor can be used.
Detail information of currently supported settings are provided in [observation files](https://github.com/ska-sa/astrokat/wiki/Observation-file).

