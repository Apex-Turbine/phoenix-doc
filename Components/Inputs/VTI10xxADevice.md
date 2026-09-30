## VTI10xxA Device
## Settings

- Host (`host`)
  - IP address of the EX device
  - Default: `192.168.0.100`
- Reset (`reset`)
  - Whether to reset the EX device during setup
  - Default: `false`
- Timezone Offset Hours (`timezone_offset_hours`)
  - Offset in hours applied to device time before validation
  - Examples: `5` for UTC+5, `-5` for UTC-5
  - Default: `0`

___
## Phoenix API
___
### Description

Connects to a VTI 10xxA device, discovers available channels, and publishes entry data streams.

### I/O

Receives connection and synchronization settings.

Produces enabled VTI channel streams.

### JSON Setup Keys

Component specific global keys:
- host
  - Description: The IP address of the EX device
  - Type: string
  - Default: "192.168.0.100"
- reset
  - Description: Reset the EX device
  - Type: boolean
  - Default: false
- timezone_offset_hours
  - Description: Timezone offset in hours (e.g., 5 for UTC+5, -5 for UTC-5). Device time will be adjusted by this offset before validation.
  - Type: number
  - Default: 0
