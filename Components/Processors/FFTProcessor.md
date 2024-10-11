# FFT Processor
## Settings

- Block Size
	- How many samples to use in each FFT pass
	- Default, 1024

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

___
# Phoenix API
___
## Description

Performs buffering and windowing of input samples, and submits them to a floating-point FFT library. 

## I/O

Receives numeric vector compatible data.

Produces single precision complex vectors containing FFT result.

## JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

Component specific input keys:
- fft_block_size
	- Number of samples from input data used to calculate FFT
	- Options: 128, 256, 512, 1024, 2048, 4096, 8192, 16384, 32768, 65536, 131072
	- Default, 1024
- fft_overlap
	- The % Overlap for the FFT
	- Default, 0
- fft_zoom
	- The % Zoom for the FFT
	- Default, 100
- fft_window
	- The window function applied to the FFT
	- Options: "None","Hamming","Blackman-Harris","Nuttall","Blackman","Flat-Top"
	- Default, "Blackman-Harris"
- fft_scaling
	- The FFT Peak scaling method
	- Options: "P2P","Peak","RMS","Average"
	- Default, "P2P"
