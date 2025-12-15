## Stream Selection
## Settings

- Data Type:
  - Specifies the type of data contained in the stream
  - Deafault, Number
- Container Type:
  - Indicates how the data is organized, such as a single value or a vector of values.
  - Default, Vector
- Reference Domain
  - Defines the domain or axis the data is referenced to
  - Default, Time
- Primary Units:
  - The measurement units associated with the primary data
  - Deafault, g

### Functionality

- Add
  - Allows the user to manually add a stream selection
- Remove 
  - Allows the user to delete a selected stream from the list
- Load
  - Allows the user to import a list of preexisting streams from a data file for selection.
___
## Phoenix API
___
### Description

Selects and forwards specific data streams from based on user-defined criteria.

### I/O

Receives multiple input data streams.

Outputs only the selected streams.

### JSON Setup Keys

Component specific global keys:
- stream_names
  - Type: array
  - Items: {Type: String}
  - Description: The names of the streams to be processed
- stream_domain
  - Type: String
  - Description: The domain of the streams to be processed
  - Default: Time calculated in seconds
- stream_units
  - Type: String
  - Description: The units of the streams to be processed
  - Default: Volts
- stream_dtype
  - Type: String
  - Description: The data type of the streams to be processed
  - Default: Number
- stream_mtype
  - Type: String
  - Description: The message type of the streams to be processed
  - default": Vector