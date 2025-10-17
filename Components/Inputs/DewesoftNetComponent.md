## Dewesoft
## Settings

- Connection Type
  - Type of Dewesoft connection  acquiring data
  - Default, DCOM
- Setup File
  - Path to Dewesoft setup configuration file

### Functionality

- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Connects to Dewesoft data acquisition systems for real-time data streaming.

### I/O

Receives real-time measurement data streams from Dewesoft acquisition channels.

### JSON Setup Keys

Component specific global keys:
- dewesoftnet_address
  - Description: The IP address of the Datatel Web GUI
  - Type: string
  - Default: ""
- dewesoftnet_type
  - Description: The type of the DewesoftNet device
  - Type: string
  - Default: DCOM
- dewesoftnet_setup
  - Description:  The setup file name to use for the Dewesoft system
  - Type: string
  - Default: ""
