## IIR Filter
## Settings
- Filter Type
    - Pass type of the filter
    - Options, Low Pass, High Pass, Band Pass, Notch, Octave, One-third Octave
    - Default, Low Pass
- Band Number (available for Octave filter types)
    - ANSI Band Number of octave band (center frequency in parentheses)
    - Default, 30 (1000 Hz)
- Frequency 1
    - Primary cutoff frequency for filter
    - Default, 5000 Hz
- Frequency 2 (available for Bandpass & Notch filter types)
    - Secondary cutoff frequency for band-type filters
    - Default, 10000 Hz
- Filter Prototype
    - Analog filter prototype
    - Options, Butterworth, Chebyshev, Inverse Chebyshev, Elliptic, Bessel
    - Default, Butterworth
- Prototype Parameters
    - Order (Order of IIR filter)
    - Default, 6
- Passband Ripple (available for Chebyshev prototype)
    - Maximum ripple loss in passband
    - Default, 0.010 dB
- Stopband Ripple (available for Inverse Chebyshev prototype)
    - Maximum ripple loss in stopband
    - Default, 48.000 dB
___
## Phoenix API
___
### Description

Applies an Infinite Impulse Response (IIR) filter to input data.

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
- iir_specification
  - Type: string
  - Description: Indicator for means by which IIR filter is specified
  - Enum: ["Internal", "File"]
- iir_filepath
  - Type: string
  - Description: The name of the filter file to load and use
- iir_pass_type
  - Type: string
  - Description: The type of filter to use
  - Enum: ["Low Pass", "High Pass", "Band Pass", "Notch", "Octave", "1/3-Octave"]
  - Default: Low Pass
- iir_band
  - Type: integer
  - Description: Parameter specifying the band index of the filter relative to 1 kHz band for octave pass types
  - Default: 30
- iir_f1
  - Type: number
  - Description: The filter's primary cutoff frequency (lower cutoff for notch, band, and octave types)
  - Default: 5000
- iir_f2
  - Type: number
  - Description: The filter's 2nd specified frequency for bandpass and notch type
  - Default: 10000
- iir_order
  - Type: integer
  - Description: Number of poles the filter has
  - Default: 6
- iir_prototype
  - Type: string
  - Description: Prototype filter used to approximate ideal filter for pass type
  - Enum: ["Butterworth", "Chebyshev", "Inv. Chebyshev", "Papoulis"]
  - Default: Butterworth
- iir_passband_ripple
  - Type: number
  - Description: Parameter controlling maximum passband loss in dB
  - Default: 0.01
- iir_stopband_ripple
  - Type: number
  - Description: Parameter controlling maximum stopband gain in dB
  - Default: 48
- iir_rolloff
  - Type: number
  - Description: Adjusts length of transition band for Elliptic filters
  - Default: 0
