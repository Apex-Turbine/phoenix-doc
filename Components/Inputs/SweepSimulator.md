## Sweep Simulator
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

### Functionality
- Pull Setup
  - This button **must** be pressed to send the configuration settings to the device
___
## Phoenix API
___
### Description

Produces a sweeping pseudo-sine wave on each channel for testing. Sine wave is made of several parabolic segments.

### I/O

Produces single precision vectors for each channel.

### JSON Setup Keys

This component follows standard input keys:
* name
* units
* size

Component specific global keys:

This component follows standard input keys:
* name
* units
* size

- samplerate
  - The sample rate of the data
  - Default, 102400

- output_rate
  - The frequency at which the component should output data in Hz
  - Default, 10.0

- num_streams
  - Specify the number of streams the component should output
  - Default, 16

- freq_step_size
  - The frequency step size of the output data
  - Default, 1

### Stream Settings
- sim_scale
  - The magnitude scale of the signal
  - Default, 10.0

- sim_phase
  - The phase of the simulated signal
  - Default, 0.0

- sim_mimics
  - stream id's that this signal should mimic
