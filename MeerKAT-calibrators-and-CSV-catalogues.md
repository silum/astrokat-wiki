Definition of target catalogues for the MeerKAT telescope can either be as CSV catalogue for standard observation script, or the YAML observation files for the observation framework.

## CSV catalogue format
The CSV catalogue format is relevant for the following:
* Older MeerKAT observation using the `image.py` and `track.py` observations takes CSV catalogue files listing targets to observe as input. Also, all observation scripts available in the [katsdpscripts](https://github.com/ska-sa/katsdpscripts) repository.
* It is also the standard format for the MeerKAT observatory calibration catalogues.

The CSV observation catalogue format is very simple:
* header section consisting of observation meta-data for user information   
`# <free form user string>`
* target information as defined in [target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification)   
`name, tags, X position, Y position, [optional flux model coefficients]`


## MeerKAT standard calibrator catalogues
MeerKAT telescope provides lists of standard primary and secondary calibrators in CSV format. The official location of these files on the operational systems is `katconfig/user/catalogues`. For offline and local system use, the catalogues are made available as part of the astrokat repository, `astrokat/catalogues`.

The MeerKAT calibrator sources and catalogues can be view in the repository:
[astrokat](https://github.com/ska-sa/astrokat)`/catalogues`


MeerKAT standard calibrator catalogues follow a specific calibrator naming convention:    
`Lband-<calibrator_classification>-calibrators.csv`   
and relevant calibrators will be added for each observation per band and calibrator type.  

The following calibrator catalogues are available:
* **Complex gain calibrators**, identified in observation files using the `gaincal` tag.    
Gain calibrators are generally classified as secondary calibrators in the `astrokat` framework scripts and currently about 100 calibrator sources are provided with good sky coverage in `Lband-gain-calibrators.csv`.

* **Bandpass calibrator sources** are indicated with the `bpcal` tag.    
Bandpass calibrators are classified as primary calibrators in the `astrokat` framework scripts. These sources have good phase closure, but some are variable, `Lband-bandpass-calibrators.csv`.

* Currently MeerKAT only use 3 trusted **flux calibrator sources**.  MeerKAT has models for these sources and they can be found in `Lband-flux-calibrators.csv`.  Flux calibrators are primary calibrators and can also be used as bandpass calibrators, so when an observation selects a flux calibrator, indicated with the `fluxcal` tag, a second bandpass calibrator is not required --- unless, the flux calibrator is very far away from the observation target and an additional bandpass calibrator closer is required. When these targets are added to observation files they are indicated with both the `bpcal` and `fluxcal` tags by default.

* Similarly, MeerKAT currently has a limited set of 3 **polarisation calibration sources** listed in `Lband-polarisation-calibrators.csv`. Polarisation calibrators are indicated in the observation files using the `polcal` tag.



