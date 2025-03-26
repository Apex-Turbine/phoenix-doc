# APEX-H5 Output

### APEX-H5 Output

### Settings

* Store
  * Accessible path to write the HDF5 file
  * No default, **Must** be present or component will take no action
* Write Interval
  * How often to commit data to the database
  * Default, 1s
* Data Point Mode
  * Only write when datapoint recording is active
  * Default, disabled
* Record at Start
  * Toggles recording at start
  * Default, enabled

***

### Phoenix API

***

#### Description

Writes processed data to HDF5 databases.

#### I/O

Receives numeric vector data and database connection parameters.

Produces database entries in HDF5.

#### JSON Setup Keys

Component specific global keys:

* storename
  * Description: The URI for the datastore to open
  * Type: string
* commit\_time
  * Description: The rate at which to commit data to the database
  * Type: number
  * Default: 1.0
* datapointmode
  * Type: boolean
  * Description: Only write when datapoint recording is active
  * Default: false
* immediatestart
  * Type: boolean
  * Description: Start recording immediately
  * Default: true
