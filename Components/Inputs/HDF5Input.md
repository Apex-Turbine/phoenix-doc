## HDF5 Input
## Settings
- HDF5 Path
	- Accessible path to the HDF5 file
	- No default, **Must** be present or component will take no action
- Only Data Points
    - Filtering for data points only
    - Default, disabled
___
## Phoenix API
___
### Description

Reads data from HDF5 databases and processes it.

### I/O

Receives database connection parameters and query configurations.

Produces numeric vector data from HDF5 databases.

### JSON Setup Keys

Component specific global keys:
- uri
  - Description: The uri or filename of the HDF5 DB file to open
  - Type: string
- only_data_points
  - Description: Whether or not to load only data points
  - Type: boolean
  - Default: false
