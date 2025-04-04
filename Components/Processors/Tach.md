## Tach
## Settings

-Trigger Type
    - Type of trigger used to initiate data collection
    - Default, Digital
- PPR (Pulses per Revolution)
    - Number of pulses expected in a revolution
    - Default, 1.0
___
## Phoenix API
___
### Description

Processes tachometer signals to determine rotational speed.

### I/O

Receives tachometer signal data.

Produces rotational speed data.

### JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

Component specific global keys:
- tach_ppr
    - description: The Pulses per Revolution of the tach device
    - type: number
    - default: 1.0
- tach_trigger_type
    - description: The type of trigger
    - type: integer
    - default: 0
- max_rpm
    - description: Maximum RPM
    - type: number
    - minimum: 0.0
    - maximum: 1000000.0
    - default: 6000.0
- trig_level
    - type: number
    - description: The level to trigger at
    - default: 0.01
- trig_debounce
    - type: number
    - description: Debounce time in seconds
    - default: 0.002
- trig_inverted
    - type: boolean
    - description: Tells the processor if the tach signal is an inverted tach
    - default: false