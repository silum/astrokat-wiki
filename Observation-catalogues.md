Standard observation catalogues have a very simple structure:
* header section consisting of observation meta-data for user information   
`# <free form user string>`
* target information as defined in [target specification](https://github.com/ska-sa/astrokat/wiki/Observation-target-specification)   
`name, tags, X position, Y position, [optional flux model coefficients]`

These files are used directly by the observation scripts available in the [katsdpscripts](https://github.com/ska-sa/katsdpscripts) repository.

It is also the standard format for the MeerKAT observatory calibration catalogues, since the CSV format is easily parsed in python

> More implementation detail required, but based on usage, maybe ask one of the astronomers for assistance.