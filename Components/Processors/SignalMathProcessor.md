## Signal Math
## Settings

- Math Operations
    - List of mathematical expressions combining one or more input streams to produce a named output stream (e.g., `TestStream = CH1 + CH6`)
    - Default, blank — **must** have at least one valid operation present or the component will produce no output streams
    - Output Unit: **Automatic (same as input)** preserves the inherited value unit. Selecting a registered unit labels the expression's result without converting numeric values or validating physical dimensions. The reference (X-axis) unit is unchanged.

### Offline Multi-Matched Input Channel Selection

When used in offline analysis with input channels that match multiple database sources in a record (for example, `CH1` and `CH6` available in both `RAW` and `Scaled` sources):
- Prior to building the pipeline, the source and reference selection dialog displays the **Signal Math** processor as a tree node, with a child group node for each output stream (e.g., `TestStream`) and leaf rows for each multi-matched input channel (e.g., `CH1`, `CH6`) paired with a combo box (`RAW`, `Scaled`).
- The selected source for each input channel is bound to that math expression and evaluated in its isolated pipeline.
- In the DataViewer stream tree, the computed output stream (e.g., `TestStream`) appears directly under the **Signal Math** component (since Signal Math is its origin source).
___
## Phoenix API
___
### Description

Evaluates algebraic and mathematical expressions across one or more synchronized numeric vector input streams to synthesize new derived output streams. Each output stream sets its `originSource` to the `SignalMathProcessorComponent` instance name and records all referenced upstream channels in its `sources` dependency array.

### I/O

Receives numeric vector data streams from one or more upstream components.

Produces mathematically synthesized numeric vector data streams owned by the Signal Math component.

### JSON Setup Keys

Component specific global keys:
- `math_ops`
    - Description: Array of operation objects, or JSON strings encoding those objects. Each operation has `"result"` and `"expr"` fields, for example `{"result": "TestStream", "expr": "{CH1} + {CH6}", "units": "Volts"}`. The optional `"units"` string selects a registered output value unit; omitted or empty means automatic. Unknown units are rejected during import.
    - Type: array
    - `$require_import`: true

Per-stream resolved settings (offline perturbation expansion):
- `resolved_math_expression`
    - Description: Optional per-stream expression override injected by `OfflineQueryComponentManager` when an expression is expanded across disambiguated multi-source input streams.
    - Type: string
- `processor_path_label`
    - Description: Human-readable perturbation label describing the input source combination (e.g., `"CH1: RAW, CH6: Scaled"`).
    - Type: string
