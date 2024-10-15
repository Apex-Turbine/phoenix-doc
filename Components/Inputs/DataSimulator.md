# Data Simulator
## Settings
- Configuration File
  - Accessible path to the configuration JSON file
  - No default, **Must** be present or component will take no action
___
# Phoenix API
___
## Description

Simulates data streams for testing and development purposes.

## I/O

Receives configuration parameters for simulation.

Produces simulated numeric vector data.

## JSON Setup Keys

Component specific global keys:
- config_file
  - The File containing the JSON configuration for the sim
  - No default, **Must** be present or component will take no action

- output_rate
  - The rate in Hz at which the simulator will output data
  - Default, 10
