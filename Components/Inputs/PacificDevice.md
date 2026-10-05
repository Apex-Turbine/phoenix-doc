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

Connects to Pacific Instruments hardware, imports channel structure, and publishes channel and health streams. Streaming uses one receiver thread, a bounded owned-buffer queue, and one ordered processing worker.

Normal stop drains queued transfers in order before callbacks are cleared. Classified stream faults stop delivery immediately and raise a component error event.

### I/O

Receives controller connection and synchronization settings.

Produces measurement streams from Pacific hardware channels.

Streaming faults are surfaced immediately through component health with:
- `status = "error"`
- `pacific_stream_error_kind = "acquisition" | "parsing" | "delivery" | "overrun"`
- `pacific_stream_error_message`

The same classified fault is emitted through the component `EventManager` as a major error event so standard Graph / DX+DAQ error handling can react without polling device state.

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
