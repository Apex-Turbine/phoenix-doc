# VTI DSA Device
## Settings
- Address
  - IP address of the VTI hardware
  - No default, **Must** be present or component will take no action
- Slots
    - Comma separated list of slot numbers (subset of all available slots)
    - Default, blank (All slots)
#### Functionality
- SFP
  - This button can be pressed to launch VTI Setup in your default internet browser
- Pull Setup
  - This button **must** be pressed to test the connection and pull setup information from the device

#### Settings Continued
- Allow Invalid Cal
    - This is required to be checked to be able to connect and perform the measurement operation on the 4380 card
    - If you disable this and if the calibration is invalid, then it will not allow you to run. You'll get an error message on your setup step
    - Default, True
- FIFO Mode
  * Stop (the default setting)
    * If the FIFO overflows, it's going to stop the acquisition.
  * Overwrite
    * Overwrites the data in the FIFO . This prevents an overflow but allows for data loss.
  * Wait
    * Makes the FIFO wait for reading before adding more data to the fifo. This prevents data loss, but if the client can't keep up, the memory usage will increase until eventual application or system crash.
* Channel List Dropdown
  * Shows a list of all channels in the Setup (Pull Setup to populate dropdown)
  * Allows you to change settings on a per channel basis or Apply All
  #### Settings available when Channels are present
    * Function
      * selection of IEPE enables a field for setting Excitation Current
      * selection of Strain enables all available options for Strain mode (gauge resistance, gauge factor, etc.)
    * Sample Rate
      * all channels on a card must have the same sample rate
      * Default, 102.4 kHz
    * Coupling
        - Stream Coupling (AC, DC, GND)
        - Default, AC
    * Mode
        - Stream Mode (Differential, Single Ended, Pseudo Differential)
        - Default, Differential
    * Range
        - Stream Range
        - Default, 10

___
# Phoenix API
___
## Description

## I/O

## JSON Setup Keys

Component specific global keys:
- 