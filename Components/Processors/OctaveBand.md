## Octave Band
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
## Phoenix API
___
### Description

Performs octave band analysis on input data.

### I/O

Receives numeric vector data.

Produces octave band analysis results.

### JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

Component specific global keys:
### component_settings
- octbank_ratio_base
  - Type: string
  - Description: Base octave ratio for generating ANSI Octaves
  - Enum: ["Base 10", "Base 2"]
  - Default: Base 10
- octbank_bandwidth_designator
  - Type: integer
  - Description: Number of bands per octave (ex. 3 subdivides octaves into 1/3-octave bands)
  - Default: 3
- octbank_frequency_lowerbound
  - Type: number
  - Description: Minimum center frequency for included bands
  - Default: 20
- octbank_frequency_upperbound
  - Type: number
  - Description: Maximum center frequency for included bands
  - Default: 20000
- octbank_weighting_scheme
  - Type: string
  - Description: Standard weight scheme used to attenuate band outputs
  - Enum: ["None", "A", "C", "Z"]
  - Default: None
- octbank_filter_method
  - Type: string
  - Description: Method used to filter signals for each octave
  - Enum: ["FIR", "FFT", "IIR"]
  - Default: IIR

### component_stream_settings
- octbank_stat_mode
  - Type: string
  - Description: Statistic that processor will compute
  - Enum: ["max", "min", "avg", "pk", "p2p", "rms", "sig_energy", "sig_power"]
  - Default: rms
