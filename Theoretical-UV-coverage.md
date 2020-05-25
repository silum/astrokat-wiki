Theoretical calculation of UV-coverage given input antenna positions
and assuming equal distribution of observation time over 8 hour period.


Configuration for the antenna array is specified in an input YAML file.
The file only contains a `reference` position indication to general location of the telescope,
followed by the list of `antennas` relative to the telescope reference position.    
The MeerKAT array configuration file look like:    
```
reference:
  latitude: '-30:42:47.4'
  longitude: '21:26:38.0'
  altitude: 1060.0
antennas:
  - name=m000, diameter=13.5, north=-8.21, east=26.797, up=0.117
  - name=m001, diameter=13.5, north=11.683, east=62.279, up=-0.028
  - name=m002, diameter=13.5, north=-32.073, east=-12.567, up=0.149
  - name=m003, diameter=13.5, north=-66.481, east=31.783, up=-0.21
...
```

Default output is a standard U vs V plot    
UV-coverage sampling at `10` time slots over the 8-hour observation period
for a target at `-30` degree declination.
```
astrokat-uvcoverage.py --config config/mkat_antennas.yml --time 10 --dec -30
```

Additional graphs
* UV mask and synthesize beam, natural weighting without a taper by adding the `--natural` argument to the instruction set
* UV mask and synthesize beam, natural weighting with Gaussian taper by adding the `--gaussian` argument to the instruction set


Example generating all output graphs and display generated coverage maps, `-v`
```
astrokat-uvcoverage.py --config config/mkat_antennas.yml --time 10 --dec -30 -v -s --natural --gaussian
```

PSF for only core antennas
```
astrokat-uvcoverage.py --config config/mkat_antennas.yml --time 300 --dec -60 -v --natural --gaussian --sub m002 m000 m005 m006 m001 m003 m004 m018 m020 m017 m015 m029 m021 m007 m019 m009 m016 m011 m028 m012 m027 m034 m035 m014 m042 m022 m013 m036 m008 m031 m026 m047 m030 m010 m032 m041 m037 m023 m038 m043 m039 m040 m024 m025
```

