## SQLite Output
## Settings

- Store
	- Accessible path to write the SQLite file
	- No default, **Must** be present or component will take no action
- Commit Time
    - Updates per second
    - Default, 1
- Enable Monitoring
    - Toggles monitoring for the SQLite connection
    - Default, enabled
- Record at Start
    - Toggles recording at start
    - Default, enabled
___
## Phoenix API
___
### Description

Writes processed data to SQLite databases.

### I/O

Receives numeric vector data and database connection parameters.

Produces database entries in SQLite.

### JSON Setup Keys

This component follows standard input keys:
- sourcename
- streamid

Component specific global keys:
- storename
  - Description: The URI for the datastore to open
  - Type: string
- commit_time
  - Description: The rate at which to commit data to the database
  - Type: number
  - Default: 1.0
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
- journal_mode
  - Description: The Journal mode to be used for the DB. Please reference SQLite Documentation.
  - Type: string
  - Enum: [WAL, MEMORY, PERSIST, TRUNCATE, DELETE, OFF]
  - Default: WAL
- sync
  - Description: The setting for Journal Synchronization. Please reference SQLite Documentation.
  - Type: string
  - Enum: [OFF, NORMAL, FULL, EXTRA]
  - Default: OFF
- tempstore
  - Description: The setting for the location of temporary tables. Please reference SQLite Documentation.
  - Type: string
  - Enum: [DEFAULT, MEMORY, FILE]
  - Default: MEMORY
- threads
  - Description: The max number of threads to be used for a prepared statement. Please reference SQLite Documentation.
  - Type: integer
  - Default: -1
- datapointmode
  - Description: Only write when datapoint recording is active
  - Type: boolean
  - Default: false
- immediatestart
  - Description: Start recording immediately
  - Type: boolean
  - Default: true
