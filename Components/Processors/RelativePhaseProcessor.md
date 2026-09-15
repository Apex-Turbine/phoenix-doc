## Relative Phase Processor
The Relative Phase Processor can be used to convert an FFT spectrum such that
the phase across spectral bins is made relative to the corresponding spectral
bin in a designated reference FFT stream. Optionally, each spectrum can be
phase-origin corrected using a designated digital trigger stream.

Both of these operations preserve magnitudes and apply rotations to adjust the
phase of each spectral bin relative to the reference or trigger.

In all cases, the original FFT reference time is preserved.

Trigger-based phase origin correction is also available in the FFT processor. It
is not recommended to use perform trigger-based phase origin correction in both
processors, as this may lead to over-correction of the phase. Both processors
use the same internal mechanism for trigger-based phase origin correction and
can be trusted to produce consistent results between them.

## Settings
- Trigger Stream: 
  - Name of a digital trigger stream providing trigger timestamps
  - Default, None
- PPR (Pulses per Revolution)
    - Number of pulses expected in a single revolution of the system
    - Default: 1.0
- Reference Stream:
  - FFT stream to use as phase reference
  - Default, None

___
## Phoenix API
___
### Description

The Relative Phase Processor adjusts FFT-derived phases relative to either a trigger source (per-pulse timestamps) and/or a designated reference FFT stream, aligning phases per frequency bin.

### I/O

Receives FFT (complex vector) streams and optional trigger or reference streams.

If both trigger-based-phase origin correction and reference-based phase
adjustment are applied, the output contains phase-origin corrected FFT streams
where the phase of each spectral bin is relative to the corresponding bin in a
time-aligned reference stream spectrum.

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
