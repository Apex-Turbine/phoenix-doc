# FFTProcessor

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
- size
	- How many samples to use in each FFT pass
	- Default, 1024
- skips
	- How many samples to move forward between each FFT pass
	- Must be greater than 0
	- Default, size
- pad
	- How many 0 samples to pad onto the end before each pass
	- Default, 0
- window
	- A window name to be applied
	- Options: Hann, Hamming, Nuttall, Blackman, Flat-Top, Blackman-Harris
	- Default, no window
- single
	- Set flag to only use a single sample out of each incoming message
	- Default, off
- singleid
	- Set the index to use if in single value mode
	- No default, **Must** be specified to use single mode

[Back](PhoenixComponents.md)

