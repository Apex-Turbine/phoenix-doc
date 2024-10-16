## Poly Processor
### Settings
___
## Phoenix API
___
### Description

Applies a polynomial transform to incoming messages on an element-wise basis.

### I/O

Receives numeric vector compatible data.

Produces single precision vectors containing polynomial mapped result.

### JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename
- size

Component specific input keys:
- poly_poly
  - Description: The array of polynomial coefficients used for applying to the specified stream. The array size determines order. i.e 1=0th order, 2=1st order, 3=2nd order
  - Type: array
  - Items:
    - Type: number
- poly_units
  - Description: Units to change to
  - Type: string
- poly_user
  - Description: User saved settings for the polynomial
  - Type: string