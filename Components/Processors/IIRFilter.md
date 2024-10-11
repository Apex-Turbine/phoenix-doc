# IIR Filter
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
# Phoenix API
___
## Description

## I/O

## JSON Setup Keys

Component specific global keys:
- iir_specification
  - Indicator for means by which IIR filter is specified
  - Options: "Internal", "File"

- iir_filepath
  - The name of the filter file to load and use

- iir_pass_type
  - The type of filter to use
  - Options: "Low Pass", "High Pass", "Band Pass", "Notch", "Octave", "1/3-Octave"
  - Default: "Low Pass"

- iir_band
  - Parameter specifying the band index of the filter relative to 1 kHz band for octave pass types
  - Default: 30

- iir_f1
  - The filter's primary cutoff frequency (lower cutoff for notch, band, and octave types)
  - Default: 5000

- iir_f2
  - The filter's 2nd specified frequency for bandpass and notch type
  - Default: 10000

- iir_order
  - Number of poles the filter has
  - Default: 6

- iir_prototype
  - Prototype filter used to approximate ideal filter for pass type
  - Options: "Butterworth", "Chebyshev", "Inv. Chebyshev", "Papoulis"
  - Default: "Butterworth"

- iir_passband_ripple
  - Parameter controlling maximum passband loss in dB
  - Default: 0.01

- iir_stopband_ripple
  - Parameter controlling maximum stopband gain in dB
  - Default: 48

- iir_rolloff
  - Adjusts length of transition band for Elliptic filters
  - Default: 0
