## Datatel
### Settings
- Address
  - IP address of the DS instance
  - Default, 192.168.50.53:80

##### Functionality
- Datatel Setup
  - This button can be pressed to launch Datatel Setup in your default internet browser
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Interfaces with Datatel systems to retrieve and process data.

### I/O

Receives connection parameters and query configurations.

Produces numeric vector data retrieved from Datatel systems.


### JSON Setup Keys

Component specific global keys:
- ip
  - Description: The IP address of the Datatel Web GUI
  - Type: string
  - Default: 192.168.50.53
- port
  - Description: The port of the webserver
  - Type: integer
  - Default: 80
- output_rate
  - Description: The rate at which the data is output in Hz
  - Type: number
  - Default: 10
