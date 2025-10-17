## Signal Math
## Settings

- Math Operations
    - Array of polynomial coefficients to apply to input streams (array size determines polynomial order)
    - Default, blank **must** have an operation present or component will take no action
___
## Phoenix API
___
### Description

Applies polynomial mathematical operations to input data streams using configurable coefficients.

### I/O

Receives numeric vector data streams.

Produces mathematically processed numeric vector data streams.

### JSON Setup Keys

Component specific global keys:
- math_ops
    - Description: The array of polynomial coefficients used for applying to the specified stream. The array size determines order. i.e 1=0th order, 2=1st order, 3=2nd order
    - Type: array
    - $require_import: true