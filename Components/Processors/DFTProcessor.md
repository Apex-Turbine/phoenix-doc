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

## I/O

## JSON Setup Keys

Component specific global keys:
- dft_resolution
  - The desired frequency resolution of the DFT
  - Default: 100

- dft_overlap
  - The % Overlap for the DFT
  - Default: 0

- dft_zoom
  - The % Zoom for the DFT
  - Default: 100

- dft_window
  - The window function applied to the DFT
  - Options: "None", "Hamming", "Blackman-Harris", "Nuttall", "Blackman", "Flat-Top"
  - Default: "Blackman-Harris"

- dft_scaling
  - The DFT Peak scaling method
  - Options: "P2P", "Peak", "RMS", "Average"
  - Default, "P2P"