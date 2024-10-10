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
- 