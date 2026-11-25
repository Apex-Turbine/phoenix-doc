# C++ Data Types, Time Sync, Units, Limits & Virtual Sensors

Foundational primitives in `PhoenixCore` guarantee unit safety, nanosecond UTC timing, zero-copy buffer movement, and structural digital twin interpolation across every pipeline stage.

## 1. `Message` & `Sample<T>`

- **`Message` ([full spec](../PhoenixCore/Message.md))**: Zero-copy smart-buffer (`std::shared_ptr<std::string>`) envelope carrying a **30-byte fixed header** (`m_srcidx`, `m_streamidx`, `m_datatype`, `m_msgtype`, 64-bit `m_tstamp` UTC nanoseconds, 32-bit `m_tdur` duration nanoseconds, float32 `m_delta`, payload size `m_sz`, polymorphic header size `m_hsz`).
  - Encodes scalars, vectors, matrices, strings, complex spectra (`DATA_CFLOATS`, `DATA_CDOUBLES`), video frames, modal fit solutions (`DATA_SOLUTION_FITS`), and hierarchical **composite trees** (`DATA_MESSAGES`, bundling multiple statistical sub-messages into a single queued envelope).
  - Carries built-in limit states (`GOOD`, `WARN`, `ALERT`).
- **`Sample<T>` (`PhoenixCore/DataTypes/Sample.hpp`)**: Floating-point value + `uint64_t` nanosecond domain pair (`std::floating_point T = double`):
  - `Sample(start_ns, delta_s, sampleIdx, val)` computes exact rounded nanosecond timestamps via `SampleUtils::roundToNearestNanosecond`.
  - `fromReference(domainReference)` creates zeroed-offset copies safely; `applyDomainShift(shift)` shifts timestamps with underflow protection.

## 2. Units & Dimensional Conversions (`Units.hpp`)

`Unit` and `UnitsTable` (`PhoenixCore/DataTypes/Units.hpp`) validate engineering unit compatibility before streams connect (`Component::checkUnits`) and apply fast affine or domain-derivative/integral conversions:

- **Unit Systems**: `SI_SYSTEM` (`0`), `USC_SYSTEM` (`1`), `IMPERIAL_SYSTEM` (`2`), `MIXED_SYSTEM` (`3`), `OTHER_SYSTEM` (`4`).
- **Affine Conversion**: Converts to/from SI base units using `(scale, offset)` — e.g. Celsius (`1.0`, `+273.15`) vs Fahrenheit (`5.0/9.0`, `+255.3722`) to Kelvin.
- **High-Throughput Array Functors**:
  - `unit(targetUnit, outPtr, inPtr, count)` converts contiguous `float*` or `double*` buffers in-place or out-of-place.
  - `UnitsTable::getUnit(name)`, `UnitsTable::availableUnits(domain, system)`, and `Unit::singleDomainDifference(...)` power unit-aware DSP graphs.

## 3. High-Precision Time Synchronization (`DataTypes/Time/`)

All Phoenix timestamps use 64-bit nanoseconds since Unix epoch UTC (`CommonTimePoint`, `PhoenixTime`):
- **`PhoenixPTP`**: Precision Time Protocol synchronization tracking.
- **`PhoenixIRIG` & `IRIGProcessor`**: Hardware IRIG-B timecode decoding and `TimecodeReference` alignment.
- **`PhoenixTimeAdapter` & `TimeAligner`**: Align heterogeneous hardware and software sample streams onto a common monotonic nanosecond baseline.

## 4. Limit Files (`DataTypes/LimitsFile/`)

Manage spectral, modal, order, and static limit envelopes (`ModalLimitParameters`, `OrderLimitParameters`, `StaticLimitParameters`, `InterpolationJson`, `MonitoredLimitsStruct`):
- **`LimitsFileJson`**: Load and serialize structured limit curves and alert thresholds.
- **`LimitsFileCSV`**: Import/export tabular engineering limit curves from CSV files.

## 5. Signal Math, ZMQ & Event Infrastructure (`Utilities/`)

- **`SignalMath` (`Utilities/Math.hpp`), `Vector*`, `FFTUtils`**: Vectorized elementwise math (`vadd`, `vsub`, `vmul`, `vdiv`, `vpoly`, `vconv`, `vminmax`, `vabs`, `vtrig`), polynomial fitting (`VectorFit`), interpolation (`LinearInterpolator`, `NearestNeighborsInterpolator`), FFT utilities (`FFTPolicies`, `FFTSpectrum`), tach/trigger processing (`TachUtils`, `TriggerUtils`, `TriggerWindow`), and WSB strain gauges (`WSBUtils`).
- **`EventManager` ([Utilities guide](../PhoenixCore/Utilities.md))**: Structured JSON diagnostic dispatch across severities `LVL_DEBUG` (`0`), `LVL_INFO` (`1`), `LVL_WARNING` (`2`), `LVL_MINOR` (`3`), `LVL_MAJOR` (`4`), and `LVL_CRITICAL` (`5`).
- **`ZMQContext` / `ZMQClient` / `ZMQServer`**: Thread-safe singleton ZeroMQ context management (`ZMQContext::instance()`, `ZMQContext::getContext(name, nthreads)`).

## 6. Virtual Sensors & Digital Twins (`Virtual/`)

Integrate 3D FEA structural meshes (`ANSYS`, `NASTRAN`, `ABAQUS`, `VTK`) with real-time telemetry (`Virtual.md`):
- **`VirtualModel`**: VTK `vtkMultiBlockDataSet` hierarchy (`Base -> Zone -> Solution Arrays` like VonMises stress or principal strain) with `vtkStaticCellLocator` spatial lookups.
- **`VirtualSensorManager` & `VirtualController`**: Perform real-time linear superposition (`SolutionFilter`) driven by live physical sensor fit factors (`SolutionFitData`) and emit virtual strain/displacement/pressure/temperature telemetry (`nodal`, `surface`, `volume`, `none` mapping constraints).

See [Virtual Subsystem](../PhoenixCore/Virtual.md) for class diagrams and adapter registration (`GetVirtuals`).
