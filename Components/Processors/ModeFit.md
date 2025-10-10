## Mode Fit
## Settings

- File Path
    - Path to JSON file specifying limits for modal analysis
    - Default, blank **must** have a JSON file or the component will take no action
- Threshold
    - Minimum threshold a peak must exceed to be considered for mode fit
    - Default, 0.0100
- Mode Fit Weight
    - Weight coefficient for modal assurance criterion in fitness calculation
    - Default, 0.5000
- Freq Fit Weight
    - Weight coefficient for frequency alignment in fitness calculation
    - Default, 0.5000
___
## Phoenix API
___
### Description

Performs modal analysis and mode fitting on input data streams using configurable thresholds and weighting parameters.

### I/O

Receives numeric vector data streams.

Produces mode fit analysis results with frequency and modal assurance criteria.

### JSON Setup Keys

Component specific global keys:
- limitsfile_limits_filepath
    - Type: string
    - Description: File path to JSON specifying limits
    - Default: ""
    - $require_import: true
- component_stream_settings
    - Type: object
    - Description: The settings for the individual specified streams
    - Properties:
        - modefit_threshold
            - Type: number
            - Description: Minimum threshold a peak must exceed to be considered for mode fit
            - Default: 0.01
        - modefit_frequency_fit_weight
            - Type: number
            - Description: Weight coefficient for frequency alignment in the overall fitness calculation
            - Default: 0.5
          - modefit_modal_fit_weight
            - Type: number
            - Description: Weight coefficient for modal assurance criterion in the overall fitness calculation
            - Default: 0.5