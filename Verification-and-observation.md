# On site observations

## Manual instructions for creating a schedule block
```
configure_obs()
obs.sb.new(owner='Observer')
obs.sb.type=katuilib.ScheduleBlockTypes.OBSERVATION
obs.sb.antenna_spec='available'
obs.sb.controlled_resources_spec='cbf,sdp'
obs.sb.desired_start_time='2018-09-14 10:05:09.406Z'
obs.sb.expected_duration_seconds='60'
obs.sb.description="Test new observation script"
obs.sb.instruction_set="run-obs-script /home/kat/usersnfs/ruby/astrokat/scripts/observe.py -o ruby --profile /home/kat/usersnfs/ruby/astrokat/test/test_targets.yaml --proposal-description='A test' "
obs.sb.to_defined()
obs.sb.to_approved()
obs.sb.unload()