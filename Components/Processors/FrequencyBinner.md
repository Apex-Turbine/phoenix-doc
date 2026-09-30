## Frequency Binner
## Settings

- Limits File Path (`freqbin_limits_filepath`)
  - Path to JSON file that defines modal frequency limits
  - Default: empty string
- Signal Statistics Enabled (`freqbin_sig_stats_enabled`)
  - Statistics to compute for signal-based limits
  - Options: `modal_freq`, `modal_amp`, `limit_pct`, `fitness`, `freq_range_abs_max`
  - Default: `limit_pct`, `modal_amp`, `modal_freq`, `fitness`, `freq_range_abs_max`
- Part Statistics Enabled (`freqbin_part_stats_enabled`)
  - Statistics to compute for part-based limits
  - Options: `limit_pct`, `fitness`, `critical_location_value`
  - Default: `limit_pct`, `fitness`, `critical_location_value`
- Limit Percent Basis (`freqbin_limit_percent_basis`)
  - Value used to calculate limit percentages
  - Options: empty string, `modal_amp`, `freq_range_abs_max`
  - Default: empty string
- Warn Level (`freqbin_warn_level`)
  - Warning threshold as fraction of modal limit
  - Default: `0.7`
- Alert Level (`freqbin_alert_level`)
  - Alert threshold as fraction of modal limit
  - Default: `0.9`

### Per-Stream Settings

- Frequency Fit Weight (`freqbin_freq_fit_weight`)
  - Weight used for frequency fit in fitness calculation
  - Default: `0.5`
- MAC Fit Weight (`freqbin_mac_fit_weight`)
  - Weight used for MAC fit in fitness calculation
  - Default: `0.5`

> Note: `freqbin_freq_fit_weight` and `freqbin_mac_fit_weight` are renormalized to sum to `1.0`.

___
## Phoenix API
___
### Description

Calculates per-mode and per-part frequency-bin metrics and limit status values.

### I/O

Receives supported signal/solution streams used for mode and limit calculations.

Produces frequency-bin statistics, fitness values, and limit percentages.

### JSON Setup Keys

Component specific global keys:
- freqbin_limits_filepath
  - Type: string
  - Default: ""
- freqbin_sig_stats_enabled
  - Type: array[string]
  - Enum items: ["modal_freq", "modal_amp", "limit_pct", "fitness", "freq_range_abs_max"]
- freqbin_part_stats_enabled
  - Type: array[string]
  - Enum items: ["limit_pct", "fitness", "critical_location_value"]
- freqbin_limit_percent_basis
  - Type: string
  - Enum: ["", "modal_amp", "freq_range_abs_max"]
  - Default: ""
- freqbin_warn_level
  - Type: number
  - Default: 0.7
- freqbin_alert_level
  - Type: number
  - Default: 0.9

Component stream keys:
- freqbin_freq_fit_weight
  - Type: number
  - Default: 0.5
- freqbin_mac_fit_weight
  - Type: number
  - Default: 0.5
