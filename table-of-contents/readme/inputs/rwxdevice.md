# CADDMAS File Reader

### CADDMAS File Reader

### Settings

* Path
  * Accessible path to the CADDMAS file
  * No default, **Must** be present or component will take no action



***

### Phoenix API

***

#### Description

Opens an APEX CADDMAS file and reads in the packets.

#### I/O

Produces single precision vectors for both APEX signals. Parameters only used for time currently.

#### JSON Setup Keys

Component specific global keys:

* filename
  * Description: Accessible path to find the CADDMAS file at
  * Default: NONE, filename **must** be present or component will take no action
