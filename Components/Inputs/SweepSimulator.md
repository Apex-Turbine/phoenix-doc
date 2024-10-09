# Sweep Simulator
## Settings
- Streams
  - Number of streams
  - Default, 16
- Sample Rate
  - Rate, in Hz, that the component should produce samples for each channel
  - Default, 102.4kHz
- Frequency Step
  - Step size of frequency sweep
  - Default, 1 Hz

#### Functionality
- Pull Setup
  - This button **must** be pressed to send the configuration settings to the device
___
# Phoenix API
___
## Description

Produces a sweeping pseudo-sine wave on each channel for testing. Sine wave is made of several parabolic segments.

## I/O

Produces single precision vectors for each channel.

## JSON Setup Keys

Component specific global keys:

* samplerate
  * Rate, in Hz, that the component should produce samples for each channel.
  * Default, 102.4kHz

This component follows standard input keys:

* name
* units
* size
