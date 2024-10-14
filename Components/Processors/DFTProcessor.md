# DFT Processor
## Settings
- Resolution
	- DFT Resolution
	- Default, 1.0 Hz

- Overlap
	- The ratio of DFT size to block size. 
	- When the DFT size is greater than the base block size, multiple blocks are chained together to perform a higher resolution DFT. This increases overall signal DFT resolution and reduces averaging
	- Default, None

- Scaling
	- Used to set the DFT scaling upon transformation to the frequency domain
	- Default, P2P

- Window
	- A window name to be applied
	- Options: None, Hamming, Nuttall, Blackman, Flat-Top, Blackman-Harris
	- Default, Blackman-Harris

___
# Phoenix API
___
## Description

Performs Discrete Fourier Transform (DFT) on input samples.

## I/O

Receives numeric vector compatible data.

Produces single precision complex vectors containing DFT result.

## JSON Setup Keys

Component specific global keys:
- dft_resolution
  - Title: DFT Resolution
  - Description: The desired frequency resolution of the DFT
  - Type: number
  - Default: 100
- dft_overlap
  - Title: DFT Overlap
  - Description: The % Overlap for the DFT
  - Type: number
  - Default: 0
- dft_zoom
  - Title: DFT Zoom
  - Description: The % Zoom for the DFT
  - Type: number
  - Default: 100
- dft_window
  - Title: DFT Window
  - Description: The window function applied to the DFT
  - Type: string
  - Enum: [None, Hamming, Blackman-Harris, Nuttall, Blackman, Flat-Top]
  - Default: Blackman-Harris
- dft_scaling
  - Title: DFT Scaling
  - Description: The DFT Peak scaling method
  - Type: string
  - Enum: [P2P, Peak, RMS, Average]
