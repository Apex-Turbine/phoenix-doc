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

### Channel Hardware Settings

When connected to Pacific 6000/6700 systems, individual channel settings are dynamically populated based on card capabilities:

- `coupling` / `ac_coupling`
  - Input coupling for cards supporting AC coupling / ICP / IEPE (e.g. Model 6729, 6029, 6036, 6068, or cards with `Q42` feature `AC`).
  - `coupling` values: `"DC"`, `"AC"`.
  - `ac_coupling` values: `false` (DC), `true` (AC).
  - Controlled via `C1 C1` (AC) and `C1 C0` (DC).
  - *Note for 6729/6029*: In late firmware versions, excitation enabled with AC coupling disabled is prohibited; the hardware automatically maintains excitation and AC coupling in tandem.
- `excitation_enable`
  - Current/voltage excitation supply toggle (read/written via `C1 E0` [enable] / `E1` [disable]).
- `output_selector`
  - Wideband vs. Filtered output selector for cards supporting dual outputs (e.g. 6729/6029).
  - Values: `"WIDEBAND"`, `"FILTERED"`.
  - Read via `CE? 1` and set via `CE 1, 0` (wideband) or `CE 1, 1` (filtered).
- `gain`
  - Amplifier gain setting. Allowed discrete steps read from card calibration tables.
- `filter`
  - Anti-alias low-pass filter cutoff frequency in Hz.
- `input_mode`
  - Transducer/calibration input mode (`TR`, `VC`, `VA`, `VS`, `ZC`, `R1`, `R2`, `S1`).

