# Scanivalve Device

### Scanivalve Device

### Settings

* Device Type
  * Type of Scanivalve device product acquiring data
  * Default, DSA5000
* Address
  * Address or hostname used to identify Scanivalve device
  * No default, **Must** be present or component will take no action

#### Functionality

* Configure
  * Re-directs to Scanivalve website for setup
* Pull Setup
  * This button **must** be pressed to test the connection and pull setup information from the device

***

### Phoenix API

***

#### Description

Depending on the device type, the test will produce floating point pressure values

#### I/O

Produces floating point Vectors and Data

#### JSON Setup Keys

* scanidev\_type
  * Description: Type of Scanivalve device product acquiring data
  * Type: string
  * Enum: \[DSA5000, MPS4200]
  * Default: DSA5000
* scanidev\_address
  * Description: Address or hostname used to identify Scanivalve device
  * Type: string
  * Default: ""
