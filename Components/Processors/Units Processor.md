## Units Processor
## Settings

- Unit Settings Mode (`unit_settings_mode`)
  - Engineering unit mode for processing
  - Options: `wsb`, `tach`, `convert`, `scaling`
  - Default: `scaling`

### Per-Stream Settings

- Unit Settings Mode (`unit_settings_mode`)
  - Stream-level override of processing mode
  - Options: `wsb`, `tach`, `convert`, `scaling`
  - Default: `scaling`
- WSB Strain User (`wsbstrain_user`)
  - User-saved settings for Wheatstone Bridge Strain processing
- EU Calibrate Enabled (`eu_calibrate_enabled`)
  - Apply EU calibration polynomial to stream
  - Default: `false`
- EU Calibrate Polynomial Coefficients (`eu_calibrate_poly_coefficients`)
  - Coefficients used for polynomial conversion
- EU Calibrate Polynomial Units (`eu_calibrate_poly_units`)
  - Output units used for polynomial conversion
  - Default: `V`
- EU Calibrate Polynomial Order (`eu_calibrate_poly_order`)
  - Polynomial order, must match coefficients length
  - Minimum: `1`
  - Default: `1`
- Convert Output Units (`convert_output_units`)
  - Target units for conversion mode
- Convert Use AC Coupling (`convert_use_ac_coupling`)
  - Use AC coupling during conversion
  - Default: `false`
- Convert Integral Initial Conditions (`convert_integral_initial_conditions`)
  - Initial values for each applied integral
- Tach PPR (`tach_ppr`)
  - Pulses per revolution used for tach mode
  - Default: `1.0`
- Trigger Reference (`trigRef`)
  - Trigger reference stream
- Trigger Type (`trigType`)
  - Trigger type selector
  - Default: `0`
- Trigger Min (`trigMin`)
  - Minimum expected trigger level
  - Range: `-24.0` to `24.0`
  - Default: `0.0`
- Trigger Max (`trigMax`)
  - Maximum expected trigger level
  - Range: `-24.0` to `24.0`
  - Default: `5.0`
- Trigger Hysteresis (`trigHysteresis`)
  - Hysteresis percentage for trigger level
  - Range: `0.0` to `99.0`
  - Default: `2.5`
- Trigger Inverted (`trigInverted`)
  - Treat trigger as low-active when true
  - Default: `false`
- Trigger Debounce Source (`trigDebounceSource`)
  - Method for debounce calculations
  - Options: `Max RPM`, `Max Frequency`, `Min Period`, `Debounce Duration`
  - Default: `Debounce Duration`
- Trigger Debounce Max Frequency (`trigDebounceMaxFrequency`)
  - Max frequency used by Max Frequency debounce mode
  - Range: `0.0` to `50000.0`
  - Default: `0.0`
- Trigger Debounce Max RPM (`trigDebounceMaxRPM`)
  - Max RPM used by Max RPM debounce mode
  - Range: `0.0` to `400000.0`
  - Default: `30000.0`
- Trigger Debounce Max RPM PPR (`trigDebounceMaxRpmPPR`)
  - Pulses per revolution used with Max RPM debounce mode
  - Range: `1.0` to `360.0`
  - Default: `1.0`
- Trigger Debounce Min Period ms (`trigDebounceMinPeriod_ms`)
  - Minimum period in milliseconds for Min Period debounce mode
  - Range: `0.0` to `60000.0`
  - Default: `0.0`
- Trigger Debounce Direct ms (`trigDebounceDirect_ms`)
  - Direct debounce duration in milliseconds
  - Range: `0.0` to `6000.0`
  - Default: `0.0`

___
## Phoenix API
___
### Description

Applies unit conversion, scaling, strain processing, and tach-trigger based transformations on input streams.

### I/O

Receives numeric stream data.

Produces transformed numeric stream data with updated units and metadata.

### JSON Setup Keys

Component specific global keys:
- unit_settings_mode
  - Type: string
  - Enum: ["wsb", "tach", "convert", "scaling"]
  - Default: "scaling"

Component stream keys:
- unit_settings_mode
- wsbstrain_user
- eu_calibrate_enabled
- eu_calibrate_poly_coefficients
- eu_calibrate_poly_units
- eu_calibrate_poly_order
- convert_output_units
- convert_use_ac_coupling
- convert_integral_initial_conditions
- tach_ppr
- trigRef
- trigType
- trigMin
- trigMax
- trigHysteresis
- trigInverted
- trigDebounceSource
- trigDebounceMaxFrequency
- trigDebounceMaxRPM
- trigDebounceMaxRpmPPR
- trigDebounceMinPeriod_ms
- trigDebounceDirect_ms
