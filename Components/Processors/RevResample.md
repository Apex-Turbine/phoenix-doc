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

## I/O

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
