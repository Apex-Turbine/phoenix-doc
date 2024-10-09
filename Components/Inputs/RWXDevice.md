# RWX Reader
## Settings
- Path
	- Accesible path to the rwx file
	- No default, **Must** be present or component will take no action
#### DAQ Mode Settings
- Playback
	- Playback speed of the file
	- Default, 1x
- Loop
	- Toggle playback looping
	- Default, Off
___
# Phoenix API
___
## Description

Opens an APEX rwx file and reads in the packets.

## I/O

Produces single precision vectors for both APEX signals. Parameters only used for time currently.

## JSON Setup Keys

Component specific global keys:
- filename
	- Accesible path to find the rwx file at
	- No default, **Must** be present or component will take no action
