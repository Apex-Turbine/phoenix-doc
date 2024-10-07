# SoundDevice

## Description

Opens a system sound input device to collect data from. Only available on Windows.

## I/O

Produces 2 channels of 16-bit integer vectors.

## JSON Setup Keys

Component specific global keys:
- size
    - How many samples to send out at a time per channel
    - Default, 1024
- samplerate
    - Target rate in Hz to run the device at
    - Default, 44100
- devicename
	- Name of the sound input device to use
	- Default, system default device

[Back](PhoenixComponents.md)

