## DX+ Publisher
## Settings

- Port
    - ZMQ Port Number
    - Default, 65424
___
## Phoenix API
___
### Description

Publishes processed data to DX+ streams.

### I/O

Receives numeric vector data for publishing.

Produces data packets for DX+ streams.

### JSON Setup Keys

This component follows standard input keys:
- sourcename
- streamid

Component specific global keys:
- port
  - Description: The port for the server to listen on
  - Type: integer
  - Default: -1
- zmq_maxdelay
  - Description: Maximum delay in seconds
  - Type: number
  - Default: 0.1
- zmq_maxbuffer
  - Description: Maximum size of the output buffer in bytes
  - Type: integer
  - Default: 10000000
- zmq_maxalloc
  - Description: Maximum Allocation size
  - Type: integer
  - Default: 1000000000
- zmq_maxhistory
  - Description: Maximum history time in seconds
  - Type: number
  - Default: 60.0