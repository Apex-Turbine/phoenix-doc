## CADDMAS Reader
## Settings

* Path
  * Accessible path to the CADDMAS file
  * No default, **Must** be present or component will take no action

#### DAQ Mode Settings

* Playback
  * Playback speed of the file
  * Default, 1x
* Loop
  * Toggle playback looping
  * Default, Off

***

### Phoenix API

***

#### Description

Opens an APEX CADDMAS file and reads in the packets.

#### I/O

Produces single precision vectors for both APEX signals. Parameters only used for time currently.

#### JSON Setup Keys

Component specific global keys:
- filename
	- Description: Accessible path to find the CADDMAS file at
	- Default: NONE, filename **must** be present or component will take no action
- output_rate
	- The rate at which to output the data in Hz
	- Default, 10

### DAQ Mode Settings
- playback
	- Description: Whether or not to play the file as fast as possible or at a slower rate
	- Options: "1x", "10x", "100x", "1000x", "Max"
	- Default: "1x"
- loop
	- Description: Whether or not to loop the playback
	- Default: false
