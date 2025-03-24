## HDF5 Output
## Settings
- Store
	- Accessible path to write the HDF5 file
	- No default, **Must** be present or component will take no action
- Commit Time
    - Updates per second
    - Default, 1
- Enable Monitoring
    - Toggles monitoring for the HDF5 connection
    - Default, disabled
- Record at Start
    - Toggles recording at start
    - Default, enabled
___
## Phoenix API
___
### Description

Writes processed data to HDF5 databases.

### I/O

Receives numeric vector data and database connection parameters.

Produces database entries in HDF5.

### JSON Setup Keys

Component specific global keys:
- storename
  - Description: The URI for the datastore to open
  - Type: string
- commit_time
  - Description: The rate at which to commit data to the database
  - Type: number
  - Default: 1.0
- datapointmode
  - Type: boolean
  - Description: Only write when datapoint recording is active
  - Default: false
- immediatestart
  - Type: boolean
  - Description: Start recording immediately
  - Default: true
