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

## I/O

## JSON Setup Keys

Component specific global keys:
- poly_poly
  - The array of polynomial coefficients used for applying to the specified stream. 
  - The array size determines order. i.e 1=0th order, 2=1st order, 3=2nd order

- poly_units
  - Units to change to

- poly_user
  - User saved settings for the polynomial
