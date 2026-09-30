## PS9016 Device
## Settings

- Device Address (`ps9016_address`)
  - IP address or hostname used to identify PS9016 device
  - Default: empty string (required for live connect)
- Device Port (`ps9016_port`)
  - TCP port number for PS9016 device connection
  - Default: `9000`
  - Range: `1` to `65535`
- Scan Rate (`ps9016_rate`)
  - Data acquisition scan rate in Hz
  - Default: `10.0`
  - Range: `0.1` to `1000.0`

___
## Phoenix API
___
### Description

Connects to a PS9016 pressure scanner, configures channels, and publishes pressure streams.

### I/O

Receives network connection and scan-rate settings.

Produces pressure channel vector data streams.

### JSON Setup Keys

Component specific global keys:
- ps9016_address
  - Description: IP address or hostname used to identify PS9016 device
  - Type: string
  - Default: ""
- ps9016_port
  - Description: TCP port number for PS9016 device connection
  - Type: integer
  - Default: 9000
  - Minimum: 1
  - Maximum: 65535
- ps9016_rate
  - Description: Scan rate in Hz for PS9016 device data acquisition
  - Type: number
  - Default: 10.0
  - Minimum: 0.1
  - Maximum: 1000.0
