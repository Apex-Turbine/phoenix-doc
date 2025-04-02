---
hidden: true
---

## Sound Output
## Settings
___
## Phoenix API
___
### Description

Opens a system sound output device to write samples to. Only available on Windows.

### I/O

Receives 2 channels of numeric vectors.

### JSON Setup Keys

This component follows standard input keys:
- sourcename
- streamid

Component specific global keys:

- size
  - Description: How many samples to send out at a time per channel
  - Default: 1024
- samplerate
  - Description: Target rate in Hz to run the device at
  - Default: 44100
- devicename
  - Description: Name of the sound output device to use
  - Default: system default device
