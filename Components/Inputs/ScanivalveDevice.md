## Scanivalve Device
## Settings

- Device Type
    - Type of Scanivalve device product acquiring data
    - Default, DSA5000 Series
- Address
    - Address or hostname used to identify Scanivalve device
    - No default, **Must** be present or component will take no action

### Functionality

- Configure
  - Re-directs to Scanivalve website for setup
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Depending on the device type, the test will produce floating point pressure values

### I/O

Produces floating point Vectors and Data

### JSON Setup Keys

- scanidev_type
    - Description: Type of Scanivalve device product acquiring data
    - Type: string
    - Enum: [DSA5000, MPS4200]
    - Default: DSA5000
- scanidev_address
    - Description: Address or hostname used to identify Scanivalve device
    - Type: string
    - Default: ""
