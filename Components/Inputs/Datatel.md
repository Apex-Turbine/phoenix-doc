## Datatel
## Settings

- Address
  - IP address of the DS instance
  - Default, 192.168.50.53:80

### Functionality

- Datatel Setup
  - This button can be pressed to launch Datatel Setup in your default internet browser
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Interfaces with Datatel systems to retrieve and process data.

### I/O

Receives connection parameters and query configurations.

Produces numeric vector data retrieved from Datatel systems.

Packets in Phoenix will always accrue 64 frames of Datatel data before writing them to plots or disk. This will result in data sample sizes for the following cases:

Single Rate Systems: 107520 S/s

1. 1 RX Mode - 4 Channels: 10240 Samples per channel
2. 1 RX Mode - 10 Channels: 4096 Samples per channel
3. 2 RX Mode - 4 Channels:  5129 Samples per channel
4. 2 RX Mode - 10 Channels: 2048 Samples per channel
5. 4 RX Mode - 4 Channels:  2560 Samples per channel
6. 4 RX Mode - 10 Channels: 1024 Samples per channel

Double Rate Systems: 215040 S/s

1. 1 RX Mode - 4 Channels: 20480 Samples per channel
2. 1 RX Mode - 10 Channels: 8192 Samples per channel
3. 2 RX Mode - 4 Channels: 10240 Samples per channel
4. 2 RX Mode - 10 Channes:  4096 Samples per channel

### JSON Setup Keys

Component specific global keys:

- ip
  - Description: The IP address of the Datatel Web GUI
  - Type: string
  - Default: 192.168.50.53
- port
  - Description: The port of the webserver
  - Type: integer
  - Default: 80
- output\_rate
  - Description: The rate at which the data is output in Hz
  - Type: number
  - Default: 10
