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

Performs Order-based Fast Fourier Transform (FFT) on input data.

## I/O

Receives numeric vector data.

Produces order-based FFT results.

## JSON Setup Keys

Component specific global keys:
- order_resolution
  - Title: Order Resolution
  - Type: number
  - Description: The desired order resolution of the Order FFT
  - Default: 1
- max_order
  - Title: Maximum Order for DFT Size
  - Type: integer
  - Description: The maximum order to compute. This is used to determine FFT size
  - Default: -1
- dft_size
  - Title: DFT Block Size
  - Type: integer
  - Enum: ["128", "256", "512", "1024", "2048", "4096", "8192", "16384", "32768", "65536", "131072"]
  - Description: Number of samples from input data used to calculate FFT
  - Default: 1024
- dft_window
  - Title: DFT Window
  - Type: string
  - Enum: ["None", "Hamming", "Blackman-Harris", "Nuttall", "Blackman", "Flat-Top"]
  - Description: The window function applied to the DFT
  - Default: Blackman-Harris
- dft_scaling
  - Title: DFT Scaling
  - Type: string
  - Enum: ["P2P", "Peak", "RMS", "Average"]
  - Description: The DFT Peak scaling method
- order_list
  - Title: Order List
  - Type: array
  - Description: List of orders to compute
  - Items: number
