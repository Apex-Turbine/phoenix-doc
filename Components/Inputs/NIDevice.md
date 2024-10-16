## NI Device
### Settings
- Task Name
  - Name of NI MAX task to load
  - No default, **Must** be present or component will take no action

##### Functionality
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Opens an NI task to be run and captured. Currently has limited settings import.

### I/O

Produces double precision vectors.

### JSON Setup Keys

Component specific global keys:
- taskname
  - Description: Name of the task as shown in NIMax or similar NI software.
  - Type: string
  - No default, **Must** be present or component will take no action
- num_samples
  - Description: The number of samples to wait for and read
  - Type: integer
  - Default: 1000



