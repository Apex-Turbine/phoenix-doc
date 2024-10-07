# BT Message Structure

## Description

This is the structure generally expected out of Tip Timing sources for use elsewhere

## Layout

- Data type
  - DATA_FLOATS | CUSTOM_HEADER1
- Message type 
  - VECTOR_DATA
- Time
  - Time should be the current time at the OPR crossing for this revolution.
- Tags
  - Uses get/setDelta using tag 0. Time of 1 rotation
  - Uses get/setRevNum using tag 1.
  - Tag 2 is use is undefined.
- Header
  - size (4 bytes) = (#probes + 2) * sizeof float\
  - stage circumference (sizeof float) = stage circumference in millimeters\
  - Probe angles (#probes * sizeof float) = probe angles in Radians
- Body
  - Speed (sizeof float) = speed for this prober
  - Deflection values (#blades * sizeof float) = Deflection values for all blades, for this probe
  - Repeat above #probes times.