## SQLite Input
### Settings
- SQLite Path
	- Accessible path to the SQLite file
	- No default, **Must** be present or component will take no action
- Start Time
    - Start date and time for the query
    - Default, disabled
- End Time
    - End date and time for the query
    - Default, disabled
- Only Data Points
    - Filtering for data points only
    - Default, disabled
___
## Phoenix API
___
### Description

Reads data from SQLite databases and processes it.

### I/O

Receives database connection parameters and query configurations.

Produces numeric vector data from SQLite databases.

### JSON Setup Keys

Component specific global keys:
- uri
  - Description: The uri or filename of the SQLite DB file to open
  - Type: string
- only_data_points
  - Description: Whether or not to load only data points
  - Type: boolean
  - Default: false
- config
  - Description: Configuration for SQLite connections
  - Type: object
  - Properties:
    - threadmode
      - Description: Specifies the threading model to use in SQLite for database concurrent access
      - Type: string
      - Enum: [multi, single, serial]
      - Default: multi
    - mmap_maxsize
      - Description: Specifies the maximum size in bytes of the virtual memory space allowed to be used for access caching
      - Type: integer
      - Default: 1e9
    - mmap_size
      - Description: Specifies the size of the memory mapped space to be pre-allocated in bytes
      - Type: integer
      - Default: 500e6
