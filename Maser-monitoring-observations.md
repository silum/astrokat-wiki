Two types of targets are specified:
* observation targets of interest with accompanying gain/delay calibrators as ordered targets
* bandpass calibrators to be observed every hour

The easiest way to get started is to build a catalogue of targets of interest. Standard format for observation catalogues are CSV files listing targets:`Name, tags, RA, Decl`   
An example source catalogue for maser monitoring is provided in the [astrokat/catalogues/OH_periodic_masers_and_calibrators.csv](https://github.com/rubyvanrooyen/astrokat/blob/master/catalogues/OH_periodic_masers_and_calibrators.csv) file

Conversion of a catalogue to an initial configuration file, using the `catalogue2config.py` script, is a useful tool if the observer does not have existing configuration files available: [astrokat/config/OH_periodic_masers.yaml](https://github.com/rubyvanrooyen/astrokat/blob/master/config/OH_periodic_masers.yaml)   
`python catalogue2config.py -i OH_periodic_masers_and_calibrators.csv -o OH_periodic_masers.yaml`

This initial file can now be restructured to create a more complex observation strategy   
[astrokat/config/OH_periodic_masers_cmplx.yaml](https://github.com/rubyvanrooyen/astrokat/blob/master/config/OH_periodic_masers_cmplx.yaml)   
After editing, and before continuing to observation planning, it is always a good idea to see that the configuration file can be parsed successfully. The `readconfig.py` script will take the config file and if parsed successfully will display the observation strategy described in the file to screen   
`python readconfig.py OH_periodic_masers_cmplx.yaml`