# Octave Band
## Settings
- Octave Ratio
    - Exponent base used to define octave ratio
    - Default, Base 10
- Bandwidth Fraction
    - Fraction specifying widths of fractional-octave bands
    - Default, 1/3
- Min Center Freq
    - Minimum center frequency for octaves in filter bank
    - Default, 20.0 Hz
- Max Center Freq
    - Maximum center frequency for octaves in filter bank
    - Default, 20000.00 Hz
- Weighting Scheme
    - Options, None, Z, A, C
    - Default, None
- Stats
    - Statistics to collect for octave bands
    - Maximum
    - Minimum
    - Average
    - Peak
    - Peak to Peak
    - Root Mean Square
    - Signal Energy
    - Signal Power
___
# Phoenix API
___
## Description

## I/O

## JSON Setup Keys

Component specific global keys:
- octbank_ratio_base
  - Base octave ratio for generating ANSI Octaves
  - Options: "Base 10", "Base 2"
  - Default: "Base 10"

- octbank_bandwidth_designator
  - Number of bands per octave (ex. 3 subdivides octaves into 1/3-octave bands)
  - Default: 3

- octbank_frequency_lowerbound
  - Minimum center frequency for included bands
  - Default: 20

- octbank_frequency_upperbound
  - Maximum center frequency for included bands
  - Default: 20000

- octbank_weighting_scheme
  - Standard weight scheme used to attenuate band outputs
  - Options: "None", "A", "C", "Z"
  - Default: "None"

- octbank_filter_method
  - Method used to filter signals for each octave
  - Options: "FIR", "FFT", "IIR"
  - Default: "IIR"

- octbank_stat_mode
  - Statistic that processor will compute
  - Options: "max", "min", "avg", "pk", "p2p", "rms", "sig_energy", "sig_power"
  - Default: "rms"
