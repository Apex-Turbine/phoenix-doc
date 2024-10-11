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

## I/O

## JSON Setup Keys

Component specific global keys:
- trig_level
  - The level to trigger at
  - Default: 0.01

- trig_debounce
  - Debounce time in seconds
  - Default: 0.002

- trig_inverted
  - Tells the processor if the tach signal is an inverted tach
  - Default: false
