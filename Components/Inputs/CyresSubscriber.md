## Cyres Subscriber
## Settings

- Address
  - IP address of the Cyres Websocket Server
  - Default, 127.0.0.1:8080
- Target Path
  - Default, /cyres

### Functionality

- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Subscribes to Cyres Subscriber data streams and processes incoming data.

### I/O

Receives subscription parameters and data packets from Cyrus Subscriber streams.

### JSON Setup Keys

Component specific global keys:
- host
  - Description: The IP address of the WebSocket Server
  - Type: string
  - Default: 127.0.0.1
- port
  - Description: The port of the WebSocket Server
  - Type: integer
  - Default: 8080
- targetPath
  - Description:  The URL Path for the WebSocket server
  - Type: string
  - Default: "/cyres"
