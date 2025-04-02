## Audio Input
## Settings

- Input Device
    - Device recording audio frequency
    - No default, Must be present or component will take no action
- Sample Rate
    - Samples rate system supports
    - Default, 44100
- Mode
    - How many channels the system uses
    - Default, Stereo

### Functionality

- Pull Setup
    - This button must be pressed to test the connection and pull setup information from the device
___
## Phoenix API
___
### Description

Opens a system sound input device to collect data from. Only available on Windows.

### I/O

Produces 2 channels of 16-bit integer vectors.

### JSON Setup Keys

Component specific global keys:
- card_index
    - Description: The Index of the sound card to use
    - Type: integer
    - Minimum: 0
    - Default: 0
- samplerate
    - Description: The samplerate to use
    - Type: string
    - Default: 44100
    - Enum: [11025, 22050, 44100, 48000, 96000]
- intype
    - Description: The input type to use
    - Type: string
    - Default: stereo
    - Enum: [mono, stero]
