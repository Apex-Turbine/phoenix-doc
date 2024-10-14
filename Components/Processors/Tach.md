# Tach
## Settings
- PPR (Pulses per Revolution)
    - Number of pulses expected in a revolution
    - Default, 1.0
___
# Phoenix API
___
## Description

Processes tachometer signals to determine rotational speed.

## I/O

Receives tachometer signal data.

Produces rotational speed data.

## JSON Setup Keys

Component specific global keys:
- tach_ppr
  - Description: The Pulses per Revolution of the tach device
  - Type: number
  - Default: 1.0
