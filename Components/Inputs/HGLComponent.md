## HGL Component
## Settings

- Component Settings
  - Broadcast Port
    - UDP port used for device discovery and communication with the HGL device.
    - Default, 19999
  - Node Settings
    - Per-node configuration options
    - Default, none
- Stream Settings
  - Hardware Settings
    - Hardware-specific configuration for each stream/channel
    - Default, none
  - Node Settings
    - Per-node configuration options
    - Default, none

### Functionality

- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Provides an interface to HGL devices for automated configuration, data acquisition, and real-time streaming of measurement data.

### I/O

Device connection parameters, node/channel configuration, and acquisition commands.

Time-synchronized numeric vector data streams from all enabled channels on each connected HGL node.

### JSON Setup Keys

Component specific global keys:
- broadcast_port
  - Description: The port of the webserver
  - Type: integer
  - Default: 19999
- node_settings
  - Title: Node Settings
  - Default: []
  - Type: array
