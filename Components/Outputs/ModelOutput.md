## Model Transmit
## Settings

- Port
    - ZMQ Port Number
    - Default, Auto-assigned
___
## Phoenix API
___
### Description

Publishes model analysis results and fit data to connected model viewers and analysis systems.

### I/O

Receives model analysis data and solution fit results.

Produces model data packets for transmission to viewers and analysis tools.

### JSON Setup Keys

This component follows standard input keys:
- sourcename
- streamid

Component specific global keys:
- port
  - Description: The port for the server to listen on
  - Type: integer
  - Default: -1
