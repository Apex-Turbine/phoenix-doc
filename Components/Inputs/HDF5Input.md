## APEX-H5 Input
## Settings

- APEX-H5 Path
	- Accessible path to the APEX-H5 file
	- No default, **Must** be present or component will take no action
- Only Data Points
    - Filtering for data points only
    - Default, disabled
___
## Phoenix API
___
### Description

Reads data from APEX-H5 databases and processes it.

### I/O

Receives database connection parameters and query configurations.

Produces numeric vector data from APEX-H5 databases.

### JSON Setup Keys

Component specific global keys:
- uri
  - Description: The uri or filename of the APEX-H5 DB file to open
  - Type: string
- only_data_points
  - Description: Whether or not to load only data points
  - Type: boolean
  - Default: false
