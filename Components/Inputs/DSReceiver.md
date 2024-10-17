## DS Receiver
## Settings
- Address
  - IP address of the DS instance
  - Default, 127.0.0.1:Auto

### Functionality
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Receives data streams from DS devices.

### I/O

Receives data packets from DS devices.

Produces numeric vector data from DS devices.

### JSON Setup Keys

Component specific global keys:
- ip
  - Description: The IP address where DS is running
  - Type: string
  - Default: 127.0.0.1
- port
  - Description: The port DS will use to transmit. Negative means use any DS instance at the given IP
  - Type: integer
  - Default: -1
- output_rate
  - Description: The rate at which the component will output data in Hz
  - Type: integer
  - Default: 10
