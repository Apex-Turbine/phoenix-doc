## Static Fit
## Settings

- File Path
    - Path to JSON file specifying limits for static fit analysis
    - Default, blank **must** have a JSON file or the component will take no action
- Output Expected Signals
    - Output expected signals based on the estimated critical value. When enabled, select which expected signals to output from the list below.
    - Default, off
___
## Phoenix API
___
### Description

Performs static fit analysis on input data streams using limits specified in a JSON configuration file.

### I/O

Receives numeric vector data streams.

Produces static fit analysis results.

### JSON Setup Keys

Component specific global keys:
- limitsfile_limits_filepath
    - Type: string
    - Description: File path to JSON specifying limits
    - Default: ""
    - $require_import: true
- solfit_expected_signals_enabled
    - Type: boolean
    - Description: If true, the Static Fit Processor will output expected signals based on estimated critical value
    - Default: false
    - $require_import: true
- solfit_expected_signals_list
    - Type: array
    - Items: string
    - Description: List of expected signals to output if expected signals flag is enabled
    - Default: []
    - $require_import: true
- component_stream_settings
    - Type: object
    - Description: The settings for the individual specified streams