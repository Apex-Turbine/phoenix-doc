## Tach
## Settings

-Trigger Type
    - Type of trigger used to initiate data collection
    - Default, Digital
- PPR (Pulses per Revolution)
    - Number of pulses expected in a revolution
    - Default, 1.0
- Tachometer Level (option available if Trigger Type is Analog)
    - Threshold at which input signal activates trigger
    - Default, 0.01
- Max Speed (option available if Trigger Type is Analog)
    - Maximum speed the processor can operate at
    - Default, 29999.998575 RPM
- Trigger Inverted (option available if Trigger Type is Analog)
    - Toggle Trigger Inverted
    - Default, off
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