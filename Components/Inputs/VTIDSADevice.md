## VTI DSA Device
## Settings

- Address
  - IP address of the VTI hardware
  - No default, **Must** be present or component will take no action
- Slots
  - Comma separated list of slot numbers (subset of all available slots)
  - Default, blank (All slots)

### Functionality

- SFP
  - This button can be pressed to launch VTI Setup in your default internet browser
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device

### Settings Continued

- Allow Invalid Cal
  - This is required to be checked to be able to connect and perform the measurement operation on the 4380 card
  - If you disable this and if the calibration is invalid, then it will not allow you to run. You'll get an error message on your setup step
  - Default, True
- FIFO Mode
  - Stop (the default setting)
    * If the FIFO overflows, it's going to stop the acquisition.
  - Overwrite
    * Overwrites the data in the FIFO . This prevents an overflow but allows for data loss.
  - Wait
    * Makes the FIFO wait for reading before adding more data to the fifo. This prevents data loss, but if the client can't keep up, the memory usage will increase until eventual application or system crash.
-   Channel List Dropdown

    * Shows a list of all channels in the Setup (Pull Setup to populate dropdown)
    * Allows you to change settings on a per channel basis or Apply All

    ### Settings available when Channels are present

    * Function
      * selection of IEPE enables a field for setting Excitation Current
      * selection of Strain enables all available options for Strain mode (gauge resistance, gauge factor, etc.)
    * Sample Rate
      * all channels on a card must have the same sample rate
      * Default, 102.4 kHz
    * Coupling
      * Stream Coupling (AC, DC, GND)
      * Default, AC
    * Mode
      * Stream Mode (Differential, Single Ended, Pseudo Differential)
      * Default, Differential
    * Range
      * Stream Range
      * Default, 10

***

## Phoenix API

***

### Description

Interfaces with VTI DSA devices to retrieve and process data.

### I/O

Receives connection parameters and query configurations.

Produces numeric vector data from VTI DSA devices.

### JSON Setup Keys

Component specific global keys:

- devices
  - Description: The IP address of the VTI device
  - Type: array
  - Items:
    * host
      * Description: The IP address of the VTI device
      * Type: string
    * slots
      * Description: The slots of the VTI device
      * Type: array
      * Items:
        * Type: integer
      * Default: \[]
- allow\_invalid\_cal
  - Description: Allow invalid calibration
  - Type: boolean
  - Default: true
- enable\_simulator
  - Description: Enable the driver's simulated Device
  - Type: boolean
  - Default: false
- simulated\_model
  - Description: The model of the simulated device
  - Type: string
  - Enum: \[EX-1403, EX-1405, EMX-1434]
  - Default: EX-1403
- data\_format
  - Description: The data format of the device
  - Type: string
  - Enum: \[Fixed Point EU, Double Precision EU, Single Precision EU, Raw Counts 32-bit, Raw Counts 16-bit, Double Precision Volts, Single Precision Volts]
  - Default: Single Precision EU
- fifo\_mode
  - Description: The FIFO mode
  - Type: string
  - Enum: \[Overwrite, Stop, Wait]
  - Default: Stop

### Stream Settings

- stream\_function
  - Description: The stream function
  - Type: string
  - Enum: \[Voltage, IEPE, Charge, Strain, MIC, MIC200, Cal, Resistance]
  - Default: Voltage
- stream\_sample\_rate
  - Description: The stream sample rate
  - Type: number
  - Default: 102400
- stream\_coupling
  - Description: The coupling
  - Type: string
  - Enum: \[AC, DC, GND]
  - Default: DC
- stream\_mode
  - Description: The mode
  - Type: string
  - Enum: \[Differential, Single Ended, Pseudo Differential]
  - Default: Normal
- stream\_range
  - Description: The range
  - Type: number
  - Default: 10
- stream\_iepe\_settings
  - Description: The stream IEPE settings
  - Type: object
  - Properties:
    * excitation\_current
      * Description: The excitation value in A
      * Type: number
      * Default: 0.004
- stream\_strain\_settings
  - Description: The stream strain settings
  - Type: object
  - Properties:
    * gauge\_factor
      * Description: The gauge factor
      * Type: number
      * Default: 2.1
    * poisson\_ratio
      * Description: The poisson ratio
      * Type: number
      * Default: 0.3
    * lead\_wire\_resistance
      * Description: The resistance of the lead wire in Ohms
      * Type: number
      * Default: 0
    * gauge\_resistance
      * Description: The gauge resistance
      * Type: number
      * Default: 120
    * excitation\_type
      * Description: The excitation type
      * Type: string
      * Enum: \[Voltage, Current, None, External]
      * Default: Voltage
    * excitation\_value
      * Description: The excitation value
      * Type: number
      * Default: 5
    * bridge\_type
      * Description: The bridge type
      * Type: string
      * Enum: \[Quarter 120, Quarter 350, Quarter 1000, Half Bending, Half Poisson, Full Bending, Full Poisson, Full Poisson Bending, Two Wire Ohm, Four Wire Ohm]
      * Default: Full Bridge
    * completion\_resistor
      * Description: The completion resistor
      * Type: string
      * Enum: \[120 Ohm, 350 Ohm, 1000 Ohm]
      * Default: 350 Ohm
    * shunt\_source
      * Description: The shunt source
      * Type: string
      * Enum: \[None, Internal, External, Internal 47K, Internal 310K, Remote]
      * Default: None
    * completion\_resistance
      * Description: The completion resistance
      * Type: number
      * Default: 0
    * shunt\_resistance
      * Description: The shunt resistance
      * Type: number
      * Default: 0
