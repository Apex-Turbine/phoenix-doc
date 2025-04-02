## FDFT Processor
## Settings

* Mode
  * Select which processor mode to use
  * Options: FFT, DFT
  * Default, FFT
* Block Size (FFT mode)
  * Number of data points used in each computation
  * Default, 1024
* Resolution (DFT mode)
  * How many samples to use in each DFT pass
  * Default, 1024
* Overlap
  * The ratio of FFT size to block size.
  * When the FFT size is greater than the base block size, multiple blocks are chained together to perform a higher resolution FFT. This increases overall signal FFT resolution and reduces averaging
  * Default, None
* Scaling
  * Used to set the FFT scaling upon transformation to the frequency domain
  * Default, P2P
* Window
  * A window name to be applied
  * Options: None, Hamming, Nuttall, Blackman, Flat-Top, Blackman-Harris
  * Default, Blackman-Harris
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

* name
* units
* streamid
* sourcename

Component specific input keys:

* mode
  * title: Mode
  * type: integer
  * description: The mode of the component
* fft_block_size
  * title: FFT Block Size
  * type: integer
  * enum: ["128", "256", "512", "1024", "2048", "4096", "8192", "16384", "32768", "65536","131072"]
  * description: Number of samples from input data used to calculate FFT
  * default: 1024
* dft_resolution
  * title: DFT Resolution
  * type: number
  * description: The desired frequency resolution of the DFT
  * default: 100
* overlap
  * title": "DFT Overlap
  * type: number
  * description": The % Overlap for the DFT/FFT
  * default: 0
* zoom
  * title: DFT Zoom
  * type: number
  * description: The % Zoom for the DFT/FFT
  * default: 100
* window
  * title: DFT/FFT Window
  * type: string
  * enum: ["None", "Hamming", "Blackman-Harris", "Nuttall", "Blackman", "Flat-Top"]
  * description: The window function applied to the DFT/FFT
  * default: Blackman-Harris
*scaling
  * title: DFT/FFT Scaling
  * type: string
  * enum: ["P2P", "Peak", "RMS", "Average"]
  * description: The DFT/FFT Peak scaling method