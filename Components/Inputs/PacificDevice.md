## Pacific Device
## Settings

- Host (`host`)
  - IP address or hostname of the Pacific controller
  - Default: `localhost`
- Port (`port`)
  - TCP command port of the Pacific controller
  - 6700 series default: `4086`
  - 6000 series default: `4081`
  - Schema default: `4086`
- Master Rate (`master_rate`)
  - Master MUX sample rate in Hz
  - Default: `1000.0`
- Sync Type (`sync_type`)
  - Time synchronization mode
  - Options: `HOST`, `IRIG`
  - Default: `HOST`

___
## Phoenix API
___
### Description

Connects to Pacific Instruments hardware, imports channel structure, and publishes channel and health streams.

### I/O

Receives controller connection and synchronization settings.

Produces measurement streams from Pacific hardware channels.

### JSON Setup Keys

Component specific global keys:
- host
  - Description: The IP address or hostname of the Pacific controller
  - Type: string
  - Default: "localhost"
- port
  - Description: The TCP command port of the Pacific controller (4086 for 6700 series, 4081 for 6000 series)
  - Type: integer
  - Default: 4086
- master_rate
  - Description: Master MUX sample rate in Hz across the chassis
  - Type: number
  - Default: 1000.0
- sync_type
  - Description: Time synchronization method
  - Type: string
  - Enum: ["HOST", "IRIG"]
  - Default: "HOST"
