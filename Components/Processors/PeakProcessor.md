# Peak Processor
## Settings
- Peaks
    - Number of peaks to collect
    - Default, 32 Peaks
- Threshold
    - Peak Threshold
    - Default, 0.01
- Min Freq
    - Minimum Frequency
    - Default, Min
- Max Freq
    - Maximum Frequency
    - Default, Max
___
# Phoenix API
___
## Description

Identifies and processes peaks in input data.

## I/O

Receives numeric vector data.

Produces peak analysis results.

## JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

Component specific global keys:
- peak_numpeaks
  - Description: The number of peaks to collect per data message
  - Type: integer
  - Default: 32
- peak_threshold
  - Description: The magnitude threshold that must be exceeded before a peak is considered for collection
  - Type: number
  - Default: 0.01
- peak_minfreq
  - Description: The minimum frequency allowed for peak extraction
  - Type: number
- peak_maxfreq
  - Description: The maximum frequency allowed for peak extraction
  - Type: number
