# APEX-H5 Input

### APEX-H5 Input

### Settings

* HDF5 file Path
  * Accessible path to the HDF5 file
  * No default, **Must** be present or component will take no action
* Start Time
  * Start date and time for data query
  * Default: current time
* End Time
  * End date and time for data query
  * Default: current time + one second
* Only Data Points
  * Whether or not to load only data points
  * Default: false

***

### Phoenix API

***

#### Description

Reads data from HDF5 databases and processes it.

#### I/O

Receives database connection parameters and query configurations.

Produces numeric vector data from HDF5 databases.

#### JSON Setup Keys

Component specific global keys:

* uri
  * Description: The uri or filename of the H5 file to open
  * Type: string
* only\_data\_points
  * Description: Whether or not to load only data points
  * Type: boolean
  * Default: false
