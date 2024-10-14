# PSQL Database
## Settings
- Host
	- Hostname for PostgreSQL server output store
	- Default, localhost
- Port
    - Port Number of PostgreSQL server output store
    - Default, 5432
- Username
	- Username for a user with access to server
- Password
    - Password for given username
- Database
	- Name of database for writing output
	- Default, postgres
- Data Table
    - Name of table to write output
    - Default, APEX_DATA
- Insert Method
	- PostgreSQL Insert Method
	- Default, Standard
- Connections
    - Maximum number of connections for writing to database
    - Default, 1
- Update Rate
	- Rate of updates to database per second
	- Default, 1
- Enable Monitoring
    - Toggles monitoring for PostgreSQL connection
    - Default, enabled
- Record at Start
    - Toggles recording at start
    - Default, enabled
___
# Phoenix API
___
## Description

Writes processed data to PostgreSQL databases.

## I/O

Receives numeric vector data and database connection parameters.

Produces database entries in PostgreSQL.

## JSON Setup Keys

Component specific global keys:
- uri
  - Description: The URI for the datastore to open
  - Type: string
- host
  - Description: The host for the datastore to open
  - Type: string
  - Default: localhost
- port
  - Description: The port for the datastore to open
  - Type: integer
  - Default: 5432
- username
  - Description: The username for the datastore to open
  - Type: string
  - Default: postgres
- password
  - Description: The password for the datastore to open
  - Type: string
- dbname
  - Description: The database name for the datastore to open
  - Type: string
  - Default: postgres
- datatable
  - Description: The Table to be used for data
  - Type: string
  - Default: APEX_DATA
- executiontype
  - Description: The execution type of the store
  - Type: string
  - Enum: [Standard, Pipeline, Copy]
  - Default: Standard
- datapointmode
  - Description: Only write when datapoint recording is active
  - Type: boolean
  - Default: false
- immediatestart
  - Description: Start the recording immediately
  - Type: boolean
  - Default: true
- commit_time
  - Description: The time to commit the data in seconds
  - Type: number
  - Default: 1
- num_connections
  - Description: The number of connections to the database
  - Type: integer
  - Default: 0
