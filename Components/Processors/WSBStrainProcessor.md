## Wheatstone Bridge - Strain
## Settings

### Strain

- Gage Resistance
  - Nominal gage resistance
  - Default, 350 Ohms
- Gage Factor
  - Strain gage factor
  - Default, 2.0
- Excitation
  - Excitation voltage/current (V, mV, uV, A, mA, uA)
  - Default, 5.0 V
- Bridge Configuration
  - Wheatstone bridge wiring used by the sensor (Quarter Bridge, Half Bridge, Full Bridge)
  - Default, Quarter Bridge
- Gage Configuration
  - Gage arrangement/preset for the selected bridge configuration (e.g. Full Bridge (all gages coplanar), Half Bridge Axial, Full Bridge Torsion). Available options depend on the bridge configuration.
  - Default, Quarter Bridge
- Lead Wire Resistance
  - Lead-wire resistance used for lead-resistance compensation
  - Default, 0 Ohms
- Strain Units
  - Units for output strain data (strain, millistrain, microstrain)
  - Default, microstrain

### Material Properties

- Poisson's Ratio
  - Poisson's ratio of the material. Required for Poisson bridge configurations.
  - Default, 0.00
- Young's Modulus
  - Young's modulus of the material, with selectable stress units (psi, ksi, MPa, GPa). Used for the strain-to-stress conversion when Calculate Stress is enabled.
  - Default, 0.00 ksi

### Output Options

- Calculate Stress
  - Output stress in addition to strain
  - Default, off
___
## Phoenix API
___
### Description

Calculates strain using bridge voltages from Wheatstone bridge circuits in various configurations.

### I/O

Receives bridge voltage vector data (in volts).

Produces strain vector data (and stress vector data when stress conversion is enabled).

### JSON Setup Keys

This component follows standard input keys:

- name
- units
- streamid
- sourcename

Component specific input keys:

- wsbstrain_user
  - type: string
  - description: User saved settings for the Wheatstone Bridge Strain Processor
- wsbstrain_strain_units
  - type: string
  - description: Strain units output by the Wheatstone Bridge Strain Processor
- wsbstrain_stress_units
  - type: string
  - description: Stress units output by the Wheatstone Bridge Strain Processor
