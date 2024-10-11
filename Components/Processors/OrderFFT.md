# Order FFT
## Settings
- Block Size
	- Angle-Domain Block Size
	- Default, 1024

- Scaling
	- Spectrum Scaling Mode
	- Default, P2P

- Window
	- Function
	- Options: None, Hamming, Nuttall, Blackman, Flat-Top, Blackman-Harris
	- Default, Blakman-Harris

- Order Resolution
	- Default, 0.500

- Orders
	- List of orders to output
	- Default, All (blank)

- Max Order
    - **Read-only display**
    - Based on Block Size and Order Resolution
    - Default, 256
___
# Phoenix API
___
## Description

## I/O

## JSON Setup Keys

Component specific global keys:
- order_resolution
  - The desired order resolution of the Order FFT
  - Default: 1

- max_order
  - The maximum order to compute. This is used to determine FFT size
  - Default: -1

- dft_size
  - Number of samples from input data used to calculate FFT
  - Options: 128, 256, 512, 1024, 2048, 4096, 8192, 16384, 32768, 65536, 131072
  - Default: 1024

- dft_window
  - The window function applied to the DFT
  - Options: "None", "Hamming", "Blackman-Harris", "Nuttall", "Blackman", "Flat-Top"
  - Default: "Blackman-Harris"

- dft_scaling
  - The DFT Peak scaling method
  - Options: "P2P", "Peak", "RMS", "Average"

- order_list
  - List of orders to compute
