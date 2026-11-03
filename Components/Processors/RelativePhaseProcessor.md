## Relative Phase Processor
The Relative Phase Processor can be used to convert an FFT spectrum such that
the phase across spectral bins is made relative to the corresponding spectral
bin in a designated reference FFT stream. Optionally, each spectrum can be
phase-origin corrected using a designated digital trigger stream.

Both of these operations preserve magnitudes and apply rotations to adjust the
phase of each spectral bin relative to the reference or trigger.

In all cases, the original FFT reference time is preserved.

Trigger-based phase origin correction is also available in the FFT processor. It
is not recommended to perform trigger-based phase origin correction in both
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

### Offline Multi-Matched Phase & Trigger Reference Selection
When the configured **Reference Stream** (`phase_ref_stream`) or **Trigger Stream** (`ref_trigger_stream`) matches multiple upstream sources or FFT paths in an offline database record (for example, `CH6` available as a pre-computed `FFT` in the database and also computed via `RAW -> FFT` or `Scaled -> FFT`), the pre-submit source and reference selection dialog allows the user to select which phase/trigger reference source to use via combo boxes under `Phase Reference` and `Trigger Reference`. Each selected reference is bound into the compiled subgraph for that processor.
___
## Phoenix API
___
### Description

The Relative Phase Processor adjusts FFT-derived phases relative to either a trigger source (per-pulse timestamps) and/or a designated reference FFT stream, aligning phases per frequency bin. Reference and trigger streams are resolved using `GraphJsonUtils::StreamNameMatcher`, supporting both standard stream names and offline multi-source disambiguated stream paths.

### I/O

Receives FFT (complex vector) streams and optional trigger or reference streams.

If both trigger-based phase origin correction and reference-based phase
adjustment are applied, the output contains phase-origin corrected FFT streams
where the phase of each spectral bin is relative to the corresponding bin in a
time-aligned reference stream spectrum.

### JSON Setup Keys

Component specific global keys:
- `ref_trigger_stream`
  - Description: The trigger reference stream
  - Type: String
  - Default: ""
- `ref_trigger_source`
  - Description: The trigger reference source component
  - Type: String
  - Default: ""
- `ref_trigger_ppr`
  - Description: The pulses per revolution of the tach device
  - Type: number
  - Default: 1.0
- `phase_ref_stream`
  - Description: The FFT stream used as phase reference (per-bin subtraction)
  - Type: String
  - Default: ""
- `phase_ref_source`
  - Description: Optional source component identifier for the phase reference stream
  - Type: String
  - Default: ""

Per-stream resolved settings (offline perturbation expansion):
- `resolved_phase_ref_stream` / `resolved_phase_ref_source`
  - Description: Per-stream disambiguated phase reference stream and source injected into the compiled subgraph during offline perturbation compilation.
  - Type: String
- `resolved_ref_trigger_stream` / `resolved_ref_trigger_source`
  - Description: Per-stream disambiguated digital trigger reference stream and source injected into the compiled subgraph during offline perturbation compilation.
  - Type: String
