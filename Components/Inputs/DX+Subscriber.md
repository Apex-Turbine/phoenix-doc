## DX+ Subscriber
## Settings

- Address
  - IP address of the DX+ Publisher instance
  - Default, 127.0.0.1:27026

### Functionality

- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Subscribes to DX+ data streams and processes incoming data.

### I/O

Receives subscription parameters and data packets from DX+ streams.

Produces numeric vector data from DX+ streams.

### JSON Setup Keys

Component specific global keys:
- ip
  - Description: The IP address of the Message Queue
  - Type: string
  - Default: 127.0.0.1
- port
  - Description: The port of the Message Queue
  - Type: integer
  - Default: 27026
- output_rate
  - Description: The output rate of the component in Hz
  - Type: integer
  - Default: 10
