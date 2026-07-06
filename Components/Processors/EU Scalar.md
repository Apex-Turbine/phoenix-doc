## EU Scalar
## Settings

- Mode
  - Processor mode (Strain Gage Calibration, Calibration Polynomial, Unit Conversions)
  - Default, Strain Gage Calibration

### Strain Gage Calibration Settings

- Gage Resistance
  - Nominal Gage Resistance
  - Default, 350 Ohms
- Gage Factor
  - Strain Gage Factor
  - Default, 2.0
- Excitation
  - Excitation Voltage/Current (V, mV, uV, A, mA, uA)
  - Default, 5.0 V
- Strain Units
  - Units for output data (strain, millistrain, microstrain)
  - Default, strain
- Stress Conversion
  - Output stress instead of strain
  - Default, off
- Stress Units
  - Units for output stress data (psi, ksi, MPa, GPa)
  - Default, psi
- Young's Modulus
  - Young's Modulus used for stress calculation
  - Default, 26000000

### Calibration Polynomial Settings

- Order
  - Degree of polynomial applied to input data
  - Default, 1
- Units
  - Units for output data
  - Default, V
- EU A Coefficient
  - Default, 1.0
- EU B Coefficient
  - Default, 0.0

### Calibration Polynomial Examples

Order = 1 Y = Ax + B

Order = 2 Y = Ax^2 + Bx + C

### Unit Conversions Settings

- Method
  - Conversion method used to map input units to output units
- Coupling
  - AC or DC coupling applied during conversion
- Unit System
  - Unit system of the output units
- Unit
  - Output unit to convert to

___
## Phoenix API
___
### Description

Applies engineering-unit conversions to incoming messages on an element-wise basis. The conversion applied depends on the selected mode.

### I/O

Receives numeric vector compatible data.

Produces single precision vectors containing the converted result.

### JSON Setup Keys

This component follows standard input keys:

- name
- units
- streamid
- sourcename

Component specific input keys:

- eu_scalar_mode
  - type: string
  - enum: ["strain_gage", "custom", "unit_conversion"]
  - description: The engineering unit mode for the polynomial processor
  - default: strain_gage
- strain_gage_resistance
  - type: number
  - description: The resistance of the strain gage used for the strain calculation. Required if eu_scalar_mode is set to strain_gage
  - default: 350
- strain_gage_factor
  - type: number
  - description: The gage factor of the strain gage used for the strain calculation. Required if eu_scalar_mode is set to strain_gage
  - default: 2.0
- strain_gage_excitation_value
  - type: number
  - description: The excitation value (voltage or current based on excitation type) used for the strain gage calculation. Required if eu_scalar_mode is set to strain_gage
  - default: 5.0
- strain_gage_excitation_type
  - type: string
  - description: The excitation type for the strain gage calculation. Required if eu_scalar_mode is set to strain_gage
  - default: V
- strain_gage_strain_units
  - type: string
  - description: The strain units for the output of the strain gage calculation. Required if eu_scalar_mode is set to strain_gage
  - default: strain
- strain_gage_enable_stress_conversion
  - type: boolean
  - description: Whether to enable conversion from strain to stress for the strain gage calculation. If enabled, strain_gage_youngs_modulus and strain_gage_stress_units must be provided. Optional if eu_scalar_mode is set to strain_gage
  - default: false
- strain_gage_stress_units
  - type: string
  - description: The stress units for the output of the strain gage calculation if stress conversion is enabled. Optional if eu_scalar_mode is set to strain_gage
  - default: psi
- strain_gage_youngs_modulus
  - type: number
  - description: The Young's modulus of the material for the strain to stress conversion if enabled. Optional if eu_scalar_mode is set to strain_gage
  - default: 26000000.0
- custom_poly_coefficients
  - type: array
  - items: number
  - description: The array of custom polynomial coefficients used for applying to the specified stream when eu_scalar_mode is set to custom. The array size determines order. i.e 1=0th order, 2=1st order, 3=2nd order
- custom_poly_units
  - type: string
  - description: Units to change to when eu_scalar_mode is set to custom
  - default: V
- custom_poly_order
  - type: integer
  - minimum: 1
  - description: The order of the polynomial to apply when eu_scalar_mode is set to custom. Must be consistent with the size of the custom_poly_coefficients array
  - default: 1
- unit_conversion_output_units
  - type: string
  - description: The output units to convert to when eu_scalar_mode is set to unit_conversion
- unit_conversion_use_ac_coupling
  - type: boolean
  - description: Whether to use AC coupling for the unit conversion when eu_scalar_mode is set to unit_conversion
  - default: false
- unit_conversion_integral_initial_conditions
  - type: array
  - items: number
  - description: Initial conditions for each integral performed during unit conversion. Should be an array of size equal to the number of integrals being applied
