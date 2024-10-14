# EU Scalar
## Settings
- Polynomial Specification Mode
    - Mode (Strain, Custom Polynomial)
    - Default, Strain

#### Strain Settings
- Gage Resistance
    - Nominal Gage Resistance
    - Default, 120 Ohms
- Gage Factor
    - Strain Gage Factor
    - Default, 2.0
- Excitation
    - Excitation Voltage/Current
    - Default, 5.0 V
- Units
    - Units for output data (Strain, Millistrain, Microstrain)
    - Default, Strain

#### Custom Polynomial Settings
- Order
    - Degree of polynomial applied to input data
    - Default, 1
- Units
    - Units for output data
    - Default, Volts
- EU A Coefficient
    - Default, 1.0
- EU B Coefficient
    - Default, 0.0

#### Custom Polynomial Examples
Order = 1
Y = Ax + B

Order = 2
Y = Ax^2 + Bx + C
___
# Phoenix API
___
## Description

Applies a polynomial transform to incoming messages on an element-wise basis.

## I/O

Receives numeric vector compatible data.

Produces single precision vectors containing polynomial mapped result.

## JSON Setup Keys

Component specific global keys:
- poly_poly
  - Description: The array of polynomial coefficients used for applying to the specified stream. The array size determines order. i.e 1=0th order, 2=1st order, 3=2nd order
  - Type: array
  - Items: number
- poly_units
  - Description: Units to change to
  - Type: string
- poly_user
  - Description: User saved settings for the polynomial
  - Type: string
