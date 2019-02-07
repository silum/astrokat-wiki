## Scheduling information

Given an observation target
1. Create an observation catalogue
```
astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain flux --cat-path astrokat/catalogues/ --outfile 'astrokat_catalogue.csv' --lst --datetime '2019-2-6 14:52:48' --horizon 20
```

***


### 1. Create an observation catalogue
For an identified observation target
* select standard MeerKAT calibrators if not provided
* list rise and set time in LST for all observation sources
* create a CSV catalogue listing sources and J2000 coordinates

**Note**: Current target coordinate input RA DEC only with string format coordinates

Simple usage example
```
astrokat-cals.py --target 'NGC641_03D03' '01:38:13.250' '-42:37:41.000' --cal-tags gain flux --cat-path astrokat/catalogues/ --outfile 'astrokat_catalogue.csv' --lst --datetime '2019-2-6 14:52:48' --horizon 20
```
Refer to [MeerKAT calibrator selection](https://github.com/ska-sa/astrokat/wiki/MeerKAT-calibrator-selection) for all options

The output of this script will tabulate the target and selected calibrators to screen.
If the `matplotlib` package is installed the script will also display an elevation plot for all sources.
Use the table output and plot to decide if you want to discard any calibrators, update any calibrator selection or need to manually add a calibrator if the MeerKAT standard calibrators are not suitable.
```
Observation catalogue astrokat_catalogue.csv

Observation Table for 2019/2/6 14:52:48 (UTC)
Times listed in LST for target rise and set times
Target visible when above 20.0 degrees
Sources         Class                           RA              Decl            Rise Time       Set Time        Separation      Notes
NGC641_03D03    radec target                    1:38:13.25      -42:37:41.0     19:37:49.93     7:40:11.37      61.06           separation from Sun
J0155-4048      radec gaincal                   1:55:37.06      -40:48:42.4     19:59:29.85     7:53:17.65      3.72            separation from NGC641_03D03
J0408-6545      radec bpcal fluxcal             4:08:20.38      -65:45:09.6     20:46:45.33     11:30:13.91     31.01 ***
J1939-6342      radec bpcal fluxcal             19:39:25.05     -63:42:43.6     12:30:28.09     2:51:43.91      52.49 ***
```

In the example output above, both `J0408-6545` and `J1939-6342` was added to the selection to ensure best coverage of the target LST range. But `J1939-6342` is not really needed since `J0408-6545` has LST rise and set times close to those of the target, thus `J1939-6342` can be manually removed from the output CSV file it is not needed.

