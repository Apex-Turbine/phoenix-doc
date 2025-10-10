## Time Aligner
## Settings

- Mode
    - Options, Offset and Irig
    - Time alignment mode
    - Default, Offset
- Time Offset (Available with Offset Option)
    - Time offset that will be applied to the incoming streams
    - Default, 0 h, 0 m, 0.000 sec
- Irig Stream (Available with Irig Option)
    - Name of the stream that will be used as irig
    - Default, blank

___
## Phoenix API
___
### Description

Applies time offset corrections to synchronize data streams from different sources.

### I/O

Receives numeric vector data streams.

Produces time-aligned numeric vector data streams.

### JSON Setup Keys

Component specific global keys:
- talign_mode
    - Type: string
    - Enum: ["irig", "offset"]
    - Description: The time alignment mode
    - Default: offset
    - $require_import: true
- irig_stream
    - Type: string
    - Description: The name of the stream to be used as irig
    - Default: 1
    - $require_import: true
- talign_offset
    - Type: number
    - Description: The offset to be applied to the incoming streams' timestamps
    - Default: 0
