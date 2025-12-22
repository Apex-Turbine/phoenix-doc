## Relative Phase Processor
## Settings

- Trigger Stream: 
  - Name of the trigger stream providing per-pulse timestamps
  - Default, None
- PPR:
  - Pulses per revolution; decimal allowed
  - Default, 1.0
- Reference Stream:
  - FFT stream to use as phase reference. Phases are aligned by frequency and subtracted
  - Default, None

___
## Phoenix API
___
### Description

The Relative Phase Processor adjusts FFT-derived phases relative to either a trigger source (per-pulse timestamps) and/or a designated reference FFT stream, aligning phases per frequency bin.

### I/O

Receives FFT (complex vector) streams and optional trigger or reference streams.

Outputs phase-adjusted FFT streams, aligned by trigger or reference.

### JSON Setup Keys

Component specific global keys:
- ref_trigger_stream
  - Description: The trigger reference stream
  - Type: String
  - Default: ""
- ref_trigger_source
  - Description: The trigger reference source component
  - Type: string
  - Default: ""
- ref_trigger_ppr
  - Description: The pulses per revolution of the tach device
  - Type: number
  - Default: 1.0
- phase_ref_stream
  - Description: The FFT stream used as phase reference (per-bin subtraction)
  - Type: String
  - Default: ""
