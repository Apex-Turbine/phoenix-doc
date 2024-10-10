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
- 