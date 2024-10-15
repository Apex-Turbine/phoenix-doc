# Triggers
## Settings
- Tachometer Level
    - Threshold at which input signal activates trigger
    - Default, 0.01
- Max Speed
    - Maximum RPMs the trigger can detect
    - Default, 30,000 RPM
- Trigger Inverted
    - Toggle Trigger Inverted
    - Default, off
___
# Phoenix API
___
## Description

Detects and processes trigger events in input data.

## I/O

Receives numeric vector data and trigger parameters.

Produces trigger event data.

## JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

Component specific global keys:
- trig_level
  - Description: The level to trigger at
  - Type: number
  - Default: 0.01
- trig_debounce
  - Description: Debounce time in seconds
  - Type: number
  - Default: 0.002
- trig_inverted
  - Description: Tells the processor if the tach signal is an inverted tach
  - Type: boolean
  - Default: false
