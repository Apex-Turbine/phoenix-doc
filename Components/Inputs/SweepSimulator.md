## Sweep Simulator
## Settings

- Streams
  - Number of streams
  - Default, 16
- Sample Rate
  - Rate, in Hz, that the component should produce samples for each channel
  - Default, 102.4 Hz
- Frequency Step
  - Step size of frequency sweep
  - Default, 1 Hz

#### Functionality

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

Component specific global keys:

- samplerate
  - Description:The sample rate of the data
  - Type: integer
  - Default: 102400

- output_rate
  - Description: The frequency at which the component should output data in Hz
  - Type: number
  - Default: 10.0

- num_streams
  - Description: Specify the number of streams the component should output
  - Type: integer
  - Default: 16

- freq_step_size
  - Description: The frequency step size of the output data
  - Type: integer
  - Default: 1

### Stream Settings
- sim_scale
  - Type: number
  - Description: The magnitude scale of the signal
  - Default: 10.0

- sim_phase
  - Type: number
  - Description: The phase of the simulated signal
  - Default: 0.0

- sim_mimics
  - Type: array
  - Items:
    - Type: integers
  - Description: stream id's that this signal should mimic
