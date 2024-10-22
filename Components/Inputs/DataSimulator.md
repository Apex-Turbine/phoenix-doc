### Data Simulator

<figure><img src="../../.gitbook/assets/home.png" alt=""><figcaption><p>gitbook caption</p></figcaption></figure>

### Settings

* Configuration File
  * Accessible path to the configuration JSON file
  * No default, **Must** be present or component will take no action

***

### Phoenix API

***

<figure><img src="../../.gitbook/assets/up.png" alt=""><figcaption><p>gitbook caption</p></figcaption></figure>

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
