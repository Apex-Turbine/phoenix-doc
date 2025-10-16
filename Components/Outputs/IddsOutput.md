## Model Transmit
## Settings

- Node ID: ApexDX+ 
    - Unique identifier for the DX+ instance
    - Default, 1
___
## Phoenix API
___
### Description

Publishes data to the IDDS network using node identification for multi-system coordination.

### I/O

Receives data for IDDS transmission.

Produces IDDS-formatted data packets tagged with node ID for distribution across the IDDS network.

### JSON Setup Keys

This component follows standard input keys:
- sourcename
- streamid

Component specific global keys:
- max_transactions
  - Description: The maximum number of transactions to be processed
  - Type: integer
  - Default: 20
- idds_node_id
  - Description: The IDDS node ID
  - Type: string
  - Default: ApexDX+00001
