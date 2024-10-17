## MeCalc Device
### Settings
- Address
  - IP address of the DS instance
  - Default, 192.168.0.92:8080

#### Functionality
- QAquire
  - This button can be pressed to launch QAquire in your default internet browser
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Interfaces with MeCalc devices to retrieve and process data.

### I/O

Receives connection parameters and query configurations.

Produces numeric vector data from MeCalc devices.

### JSON Setup Keys

Component specific global keys:
- ip
  - Description: The IP address of the MeCalc device
  - Type: string
  - Default: 192.168.0.92
- port
  - Description: The port of the webserver
  - Type: integer
  - Default: 8080
- output_rate
  - Description: The rate at which the data is output in Hz
  - Type: number
  - Default: 10
