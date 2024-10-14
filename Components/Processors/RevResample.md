# Rev Resample
## Settings
- Trigger Stream
    - Stream name for input channel providing trigger reference
    - No default, **Must** be present or component will take no action
- PPR (Pulses per Revolution)
    - Number of pulses expected in a revolution
    - Default, 1.0
- Angle Resolution
    - Resolution of resample with respect to angle domain
    - Default, 0.087890625
___
# Phoenix API
___
## Description

Resamples input data to a different rate.

## I/O

Receives numeric vector data and resampling parameters.

Produces resampled numeric vector data.

## JSON Setup Keys

Component specific global keys:
- ppr
  - Description: The Pulses per Revolution of the tach device
  - Type: number
  - Default: 1.0
- trigger_ref
  - Description: The trigger reference stream
  - Type: string
  - Default: ""
- angle_resolution
  - Description: The resolution in degrees of the revolution
  - Type: number
  - Default: 0.087890625
