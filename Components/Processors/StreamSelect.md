## Stream Selection
## Settings

- Type:
  - Specifies the type of data contained in the stream
  - Default, Time Waveform
- Units:
  - The measurement units associated with the primary data type
  - Default, g
- Data Type (option available if Type is Advanced)
  - The format of individual data elements
  - Default, Number
- Container (option available if Type is Advanced)
  - The organizational structure of data elements
  - Default, Vector
- Domain (option available if Type is Advanced)
  - The physical measurement domain
  - Default, Time

### Functionality

- Add
  - Allows the user to manually add a stream selection by logical name
- Remove 
  - Allows the user to delete a selected stream from the list
- Load
  - Allows the user to import a deduplicated list of preexisting streams from a database file or record. When a record contains multiple sources with the same logical stream name (e.g., `RAW` and `Scaled`), the logical name is listed once per domain/unit configuration.

### Offline Stream Resolution & Multi-Match Behavior

When an offline analysis graph is submitted against one or more database records:
1. **Five-Attribute Matching**: Each configured stream in `Stream Select` is matched against the record's stored streams by **logical name**, **domain**, **engineering units**, **message type**, and **data type**.
2. **Unavailable Streams**: If a configured stream is not present in a selected record, it is pruned from that record's execution graph and a warning notification is displayed listing the unavailable streams, while all valid streams proceed normally.
3. **Multi-Matched Streams**: If a configured stream matches multiple sources within the same record (for example, `CH1` stored under both `RAW` and `Scaled` sources), a source selection dialog prompts the user to choose which source(s) to process. Selected sources are grouped under the channel's logical name in the viewer tree and distinguished in the Plot Source Selector.
___
## Phoenix API
___
### Description

Selects and forwards specific data streams from offline database records or upstream sources based on logical name, physical domain, units, data type, and container format.

### I/O

Receives raw or processed input data streams from a database reader or parent source.

Outputs the matched streams with full database metadata (sample rate, delta, units, limits) propagated to downstream processors.

### JSON Setup Keys

Component specific global keys:
- `stream_names`
  - Type: array
  - Items: `{Type: String}`
  - Description: Logical names of the streams to be selected and processed
  - `$require_import`: true
- `stream_domain`
  - Type: String
  - Description: The physical reference domain of the streams to be processed
  - Default: Time (seconds)
- `stream_units`
  - Type: String
  - Description: The primary engineering units of the streams to be processed
  - Default: Volts
- `stream_dtype`
  - Type: String
  - Description: The data type of the streams to be processed (`number`, `complex`, `integer`, etc.)
  - Default: Number
- `stream_mtype`
  - Type: String
  - Description: The message container type of the streams to be processed (`vector`, `entry`, etc.)
  - Default: Vector
- `stream_sources`
  - Type: array
  - Items: `{logicalName: String, streamName: String, source_index: Integer, stream_index: Integer}`
  - Description: Dynamically injected by `OfflineQueryComponentManager` during subgraph compilation to bind each selected stream to its exact `(source_index, stream_index)` database coordinates.
