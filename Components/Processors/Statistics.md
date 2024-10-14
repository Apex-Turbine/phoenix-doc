# Statistics
## Settings
- Time
    - Length of time intervals used to calculate statistics
    - Default, 1.0 sec
- Stats
    - Statistics to collect on time intervals
    - Maximum
    - Minimum
    - Average
    - Peak
    - Peak to Peak
    - Root Mean Square

___
# Phoenix API
___
## Description

## I/O

## JSON Setup Keys

Component specific global keys:
- stat_mode
  - Description: The statistics mode
  - Type: string
  - Enum: ["max", "min", "avg", "pk", "p2p", "rms"]
  - Default: "p2p"
- stat_time
  - Description: The time period over which the statistics are calculated in seconds
  - Type: number
  - Default: 1
