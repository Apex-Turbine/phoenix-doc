# Data Simulator

![alt text](\_deps/boost-src/tools/auto\_index/doc/html/images/home.png)

<figure><img src="../../.gitbook/assets/home.png" alt=""><figcaption></figcaption></figure>

### Data Simulator

### Settings

* Configuration File
  * Accessible path to the configuration JSON file
  * No default, **Must** be present or component will take no action

***

### Phoenix API

***

#### Description

Simulates data streams for testing and development purposes.

#### I/O

Receives configuration parameters for simulation.

Produces simulated numeric vector data.

#### JSON Setup Keys

Component specific global keys:

* config\_file
  * The File containing the JSON configuration for the sim
  * No default, **Must** be present or component will take no action
* output\_rate
  * The rate in Hz at which the simulator will output data
  * Default, 10
