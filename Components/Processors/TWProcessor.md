# TWProcessor

## Description

Performs buffering and windowing of input samples, and submits them to a 
floating-point FFT library, followed by traveling wave analysis. 

## I/O

Receives BT formatted data.

Produces vectors of a result structure.

## JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

Component specific input keys:
- size
	- How many samples to use in each FFT pass
	- Default, 1024
- skips
	- How many samples to move forward between each FFT pass
	- Must be greater than 0
	- Default, size
- window
	- A window name to be applied
	- Options: Hann, Hamming, Nuttall, Blackman, Flat-Top, Blackman-Harris
	- Default, no window
- single
	- Set flag to only use a single blade out of each incoming message
	- Default, off
- singleid
	- Set the index to use if in single value mode
	- No default, **Must** be specified to use single mode

[Back](PhoenixComponents.md)

