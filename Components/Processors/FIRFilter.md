## FIR Filter
## Settings

- Delay
    - Duration of data collected in seconds before applying filter
    - Default, Auto
- Filter Type
    - Pass type of the filter
    - Options, Low Pass, High Pass, Band Pass, Notch, Octave, One-third Octave
    - Default, Low Pass
- Band Number (enabled if for Octave filter types)
    - ANSI Band Number of octave band (center frequency in parentheses)
    - Default, 30 (1000 Hz)
- Frequency 1
    - Primary cutoff frequency for filter
    - Default, 5000 Hz
- Frequency 2 (enabled if for Bandpass & Notch filter types)
    - Secondary cutoff frequency for band-type filters
    - Default, 10000 Hz
___
## Phoenix API
___
### Description

Applies a Finite Impulse Response (FIR) filter to input data.

### I/O

Receives numeric vector data and filter coefficients.

Produces filtered numeric vector data.

### JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

Component specific global keys:
- fir_specification
  - Type: string
  - Description: Identifier specifying whether FIR will use file or internal filter logic
  - Enum: ["File", "Internal"]
  - Default: Internal
- fir_filepath
  - Type: string
  - Description: The name of the filter file to load and use
  - Default: ""
- fir_pass_type
  - Type: string
  - Description: The type of filter to use
  - Enum: ["Low Pass", "High Pass", "Band Pass", "Notch", "Octave", "1/3-Octave"]
  - Default: Low Pass
- fir_delay
  - Type: number
  - Description: The delay of the filter in seconds. <=0 represents auto config
  - Default: 0
- fir_f1
  - Type: number
  - Description: The filter frequency
  - Default: 5000
- fir_f2
  - Type: number
  - Description: The filter 2nd specified frequency for bandpass type
  - Default: 10000
- fir_band
  - Type: number
  - Description: ANSI standard band number for octave band centers
  - Default: 30
