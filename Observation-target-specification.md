## Observation target input

Observational targets and calibrators are grouped in observational catalogues provided by the requesting astronomer or constructed in collaboration with MeerKAT astronomers.

The minimum requirement is a comma-separated file that contains a list of targets, with or without calibrators:
`<catalogue_name>.csv`

The structure of each target specified in the input catalogues can be assumed to be:
* Target name [optional]
* Tags defining target coordinate type
* Target X location
* Target Y location
* Flux model [optional]

Note: All information provided in the catalogue file must be standard text, no unicode characters are allowed.

### Target name
User free form string associated with the target.   
Sometimes a target may have a number of names associated and these can be listed using the '`|`' to separate names.   
`*J0025-2602 | PKS 0023-26 | OB-238`   
The asterisk, '`*`', character indicate the preferred target name to use.    
**Important to note** is that the standard naming convention assumed is the `J` format and some auto tasks may not run if a different naming convention is selected as preferred target name.

### Tags
A list of tags associated with the provided target coordinates.
These are generally provided by the proposing astronomer for pointing information.   
The first tag is always the format of the coordinates provided:    
`radec` or `azel`   
For targets the explicit `target` tag or epoch `B1950` can be provided. `J2000` is assumed as default epoch.

The following calibrator tags may appear in submitted/existing catalogues: `delaycal`, `gaincal`, `bpcal`, `fluxcal`, `polcal`      
**Important to note** is that these tags are used by the telescope pipelines and some MeerKAT processing software require the `delaycal` tag for secondary gain/phase calibrators.

### Target location

Specials such as planets are simply   
`Sun, special`   
and not discussed further.

The targets can be equatorial, horizontal or galactic.
* Equatorial coordinates
  * X = ra (right ascension) -- given as HH:MM:SS.f or an angle in degrees
  * Y = dec (declination) -- given as [sign]DD:MM:SS.f or an angle in degrees
* Horizontal coordinates
  * X = az (azimuth angle) -- given as an angle in degrees
  * Y = alt (altitude angle) -- given as angle above horizon

It is important to note that the target coordinates must be **astrometric**. That means the coordinates for the epoch specified.   
Maximum northern pointing angle for MeerKAT is **+33**.

## Example Targets
Targets are implemented using [katpoint](https://pypi.org/project/katpoint/), based on [PyEphem](http://rhodesmill.org/pyephem/)

```
target = 'J1331+3030 | *3C286, radec bpcal polcal, 13:31:08.288, +30:30:32.959'
target = katpoint.Target(target)
target.__dict__
{'aliases': ['J1331+3030'],
 'antenna': None,
 'body': <ephem.FixedBody '3C286' at 0x1088e22f0>,
 'flux_freq_MHz': None,
 'flux_model': None,
 'name': '3C286',
 'tags': ['radec', 'bpcal', 'polcal']}
```

```
target ='G328.24-0.55, radec B1950, 15:54:06.11, -53:50:47.0'
target = katpoint.Target(target)
target.__dict__
{'aliases': [],
 'antenna': None,
 'body': <ephem.FixedBody 'G328.24-0.55' at 0x1088e2450>,
 'flux_freq_MHz': None,
 'flux_model': None,
 'name': 'G328.24-0.55',
 'tags': ['radec', 'B1950']}
```

## Known issues
Target names or coordinates can potentially have issues if hidden or special characters were captured during a copy and paste process. Simple validation is by parsing each target provided as a `katpoint.Target` and evaluating success as shown in the examples above.

Most important fields to verify is that a `ephem` **FixedBody object** could be created, which means that the coordinates could be read and parsed, as well as the **tags** has been assigned correctly.
```
target.body
<ephem.FixedBody '3C286' at 0x1088e25b0>

target.tags
['radec', 'bpcal', 'polcal']
```

Errors can be difficult to identify, but the following list provides the most common:
* Spaces can be replaced with short space, long space or tab characters
* Name(s) specification can contain the '`|`', '`*`' and '`-`' characters prone to be replaced with special characters by word processing software.
* Target declination in the southern hemisphere requires a negative sign,'`-`', and this is most common replaced by the double dash '`--`' character and difficult to see the difference when reading the target.   
Also when C&P the negative sign of the declination coordinate can be lost, meaning the target could potentially be unreachable if it now gives a far northern target declination.
* Spelling errors can be provided by selecting from a list of tags

Most common issues slip in when provided targets are copy and paste from wordprocessors or especially from PDF documents.