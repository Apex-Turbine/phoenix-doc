## FDFT Processor
## Settings

- Mode
  - Select which processor mode to use
  - Options: FFT, DFT
  - Default, FFT
- Block Size (FFT mode)
  - Number of samples from input data used to calculate FFT
  - Default, 1024
- Resolution (DFT mode)
  - The desired frequency resolution of the DFT
  - Default, 100 Hz
- Overlap
  - The ratio of FFT size to block size.
  - When the FFT size is greater than the base block size, multiple blocks are chained together to perform a higher resolution FFT. This increases overall signal FFT resolution and reduces averaging
  - Default, None
- Scaling
  - Used to set the FFT scaling upon transformation to the frequency domain
  - Default, P2P
- Window
  - A window name to be applied
  - Options: None, Hamming, Nuttall, Blackman, Flat-Top, Blackman-Harris
  - Default, Blackman-Harris

### Trigger-based Phase Origin Correction Settings
If a digital trigger stream is provided as an additional input to the FDFT Processor, the outgoing spectra
will use the trigger timestamps as an external time-basis. Trigger-based phase origin correction
provides a coherent view into the phase evolution of the signal across time, and also enables accurate
spectral comparison across signals sharing the same time basis.

- Trigger Type
  - Indicates the type of trigger required for trigger-based phase origin
    correction. Only digital triggers are supported.
  - Default, Digital
- Trigger Stream:
  - Name of a digital trigger stream providing trigger timestamps
  - Default, None
- PPR (Pulses per Revolution)
    - Number of pulses expected in a single revolution of the system.
    - Default: 1.0
___
## Phoenix API
___
### Description

Performs buffering and windowing of input samples, and submits them to a floating-point FFT library.

### I/O

Receives numeric vector compatible data.

Produces single precision complex vectors containing FFT result.

### JSON Setup Keys

This component follows standard input keys:

- name
- units
- streamid
- sourcename

Component specific input keys:

- mode
  - title: Mode
  - type: integer
  - description: The mode of the component
- fft_block_size
  - title: FFT Block Size
  - type: integer
  - enum: ["128", "256", "512", "1024", "2048", "4096", "8192", "16384", "32768", "65536","131072"]
  - description: Number of samples from input data used to calculate FFT
  - default: 1024
- dft_resolution
  - title: DFT Resolution
  - type: number
  - description: The desired frequency resolution of the DFT
  - default: 100
- overlap
  - title": "DFT Overlap
  - type: number
  - description": The % Overlap for the DFT/FFT
  - default: 0
- zoom
  - title: DFT Zoom
  - type: number
  - description: The % Zoom for the DFT/FFT
  - default: 100
- window
  - title: DFT/FFT Window
  - type: string
  - enum: ["None", "Hamming", "Blackman-Harris", "Nuttall", "Blackman", "Flat-Top"]
  - description: The window function applied to the DFT/FFT
  - default: Blackman-Harris
*scaling
  - title: DFT/FFT Scaling
  - type: string
  - enum: ["P2P", "Peak", "RMS", "Average"]
  - description: The DFT/FFT Peak scaling method
- trigRef
  - description: The trigger reference stream
  - type: string
  - default: ""
- trigType
  - description: Trigger type. Must be digital for FFT.
  - type: integer
  - default: 1
- trigRefEnabled
  - description: Whether the trigger reference stream is enabled
  - type: boolean
  - default: false
- trigPPR
  - description: Pulses per revolution for the trigger reference
  - type: number
  - default: 1.0
- timeReference
  - description: Time reference point within the FFT block for phase alignment
  - type: string
  - enum: ["Middle", "Start"]
  - default: "Middle"