## RWX File Writer
## Settings

- Monitor Directory
	- Directory path for storing continuous monitor recordings
	- Default, /data/monitor
- Event Directory
  - Directory path for storing triggered event recordings
  - Default, /data/event
- Record at Start
  - Toggles recording at start
  - Default, disabled
- Events Only
  - Record only triggered events, not continuous data
  - Default, disabled
- Group by Sample Rate
  - Group streams with matching sample rates into single files
  - Default, disabled
___
## Phoenix API
___
### Description

Writes processed data streams to RWX file format for storage and analysis.

### I/O

Receives numeric vector data streams.

Produces RWX files containing recorded data.

### JSON Setup Keys

Component specific global keys:
- monitor_directory
  - Type: string
  - Default: /data/monitor
  - Description: The directory to place monitor recordings
  - Title: Monitor Directory
- event_directory
  - Type: string
  - Default: /data/event
  - Description: The directory to place event recordings,
  - Title: Event Directory
- immediatestart
  - Type: boolean
  - Default: false
  - Description: Immediately begin recording at start
  - Title: Record at Start
- eventmode
  - type: boolean
  - default: false
  - description: Record only data events
  - title: Events Only
- group_by_samplerate
  - Type: boolean
  - Default: false
  - Description: Group streams by sample rate when recording
  - Title: Group by Sample Rate
