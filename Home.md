Specifying terminology and functionality that is incorporated into the new observation framework in relation to observational requirements.

Most requirements and implementations are derived from usage cases provided by Commissioning.

## All observations
* [Observation target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification) of observation targets, definition, structure and coordinate specification.   
* Observation target catalogues, CSV format [catalogues](https://github.com/ska-sa/astrokat/wiki/Observation-catalogues) vs [observation files](https://github.com/ska-sa/astrokat/wiki/Observation-file).   
As well as a method to go [from CSV format catalogue to observation file](https://github.com/ska-sa/astrokat/wiki/Catalogues-to-observation-files) in order to easily move from the older observation regime to the new observation framework.
* Additional scripts provide an easy way of selecting [calibrators from MeerKAT observatory catalogues](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrator-selection) as well as some basic functionality for command line observation evaluation during planning.
* A number of [observation types](https://github.com/ska-sa/astrokat/wiki/Types-of-target-observations) are available with the standard tracking of a target the default observation type.
* Some observations may include the use of a noise diode. The current understanding of the various noise diode implementation requirements are described here.

### Noise diode usage during observations
* astrokat [issue 5](https://github.com/ska-sa/astrokat/issues/5)


### Verifcation needs
* the various steps and stages of running the script offline and online


## Correlator specific observational requirements
Information and options required during observation planning and execution in the YAML file
* Correlator observation input

Functionality represented in tag values -- why and which observations needs them
* Correlator observation functionality


## Beamformer specific observational requirements
Information and options required during observation planning and execution in the YAML file
* Display beam shape from antennas selected and UV coverage for integration time
* Beamformer observation input

Functionality represented in tag values -- why and which observations needs them
* Beamformer observation functionality
