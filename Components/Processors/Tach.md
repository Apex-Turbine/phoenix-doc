# Tach
The Tach processor computes an RPM using a PPR (Pulses Per Revolution) value
along with an analog trigger stream or a stream of trigger event timestamps.

## Settings
### Tach Settings
- PPR (Pulses per Revolution)
    - Number of pulses expected in a single revolution of the system
    - Default: 1.0

### Trigger Settings
#### Trigger Selection Settings
The following settings pertain to selecting a suitable trigger stream to provide trigger times.

- Trigger Type
  - Whether to use an Analog signal or Digital trigger timestamps to provide trigger data. If Analog is selected,
    further configuration may be required to accurately interpret the trigger signal.

- Trigger Reference Stream
  - Input stream containing trigger data. If an Analog trigger signal is selected, further configuration may be required
    to accurately interpret the signal. If a Digital stream is selected, the stream should provide nanosecond timestamps
    corresponding to trigger times.

#### Analog Trigger Signal Settings
The following settings configure how an analog trigger signal should be interpreted.
In general, the only settings that will likely need to be modified are the
"Trigger Min Level" and the "Trigger Max Level". These settings determine where the trigger
level will be placed and are also used to configure noise rejection.

- Trigger Min Level
  - The minimum expected level for the analog trigger signal
  - Default: 0 V
  - Range: [-24 V, 24 V]

- Trigger Max Level
  - Default: 5 V
  - Range: [-24 V, 24 V]

- Trigger Hysteresis
  - Percentage of peak-to-peak range to configure as a dead band. In general,
    the default should be sufficient.
    
    Incorporating triggering hysteresis can improve noise rejection in the
    resulting trigger times. Practically, the hysteresis value set here
    corresponds to a percentage of the peak-to-peak signal amplitude centered on
    the midpoint that is considered to be a "dead band." A signal with
    a level in the dead band has the same logical state that it had prior to
    crossing into the dead band, and should retain that state until it crosses
    the other state's threshold.

         
    As an example, if the trigger range is [0 V, 5 V], a typical switching level
    would be 2.5 V. If the signal is increasing in magnitude when crossing the
    switching level, the signal is considered to have a **HIGH** logical state.
    The inverse is true when the signal decreases across the switching level.
    Because the switching point is a thin boundary, even a small amount of noise
    in the signal near the switching level can lead to spurious edge detection
    and timing jitter. This can somewhat be remedied by debouncing the switching
    level crossing (rejecting any additional transitions occurring within some
    period after the initial edge), but it's not a perfect solution.
      
    Hysteresis effectively turns the switching level into a switching region,
    which means that there is far more tolerance to noise when detecting
    transitions between logical states. Practically, in our 5V peak-to-peak
    example, if we set our hysteresis value to 2.5%, we will have a dead band of
    125 mV centered at 2.5 V. The high threshold will be set to 2.5625 V, and
    the low threshold will be set at 2.4375 V. If a signal is currently **LOW**,
    it must cross the **LOW** threshold, cross the dead band, *and then* cross
    the **HIGH** threshold in order to be considered **HIGH**. At this point, we
    would interpolate to find the exact crossing time.
    
    We set a default of **2.5%**, which is generally high enough to benefit from
    the additional noise rejection without impacting results.

  - Default: 2.5%
  - Range: [0%, 99%]

- Trigger Inverted
  - If inverted, the trigger signal will be interpreted as having a low active level instead of high.
  - Default: false

- Trigger Debounce Source and Value
  - Debouncing is a noise-rejection technique that works by ignoring pulses that
    occur closer together than would be physically possible for the
    system.
    
    If electrical noise is present in the trigger signal near the threshold level,
    it is entirely possible for the trigger signal to change
    between active and inactive states rapidly, which can lead to spurious
    trigger detections.

    Several configuration methods are available for setting the debounce period,
    but in general, these are provided mostly for convenience. Unless "Debounce
    Duration" is explicitly selected, the chosen method and value are used to
    compute a minimum valid signal period, and pulses occurring within some
    fraction of that period are rejected.

  - Options:
    - Max RPM
    - Max Frequency
    - Min Period
    - Debounce Duration
  - Default: Debounce Duration

___
## Phoenix API
___
### Description
The Tach processor computes an RPM using a PPR (Pulses Per Revolution) value
along with an analog trigger stream or a stream of trigger event timestamps.

### I/O

Receives tachometer signal data.

Produces rotational speed data.

### JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename

### Component Settings:
```
    "tach_ppr": {
        "description": "The Pulses per Revolution of the tach device",
        "type": "number",
        "default":1.0
    },
```

#### Trigger Specific Settings (also in Component Settings):
The following settings are still included in component settings, but are specific to trigger signal interpretation.

```
    "trigRef": {
        "description": "The trigger reference stream",
        "type": "string",
        "default":"",
        "$require_import": true
    },
    "trigType": {
        "description": "The type of trigger",
        "type":"integer",
        "default": 0,
        "$require_import": true
    },
    "trigMin": {
        "description": "Minimum expected voltage level for the trigger signal.",
        "type": "number",
        "default": 0.0,
        "minimum": -24.0,
        "maximum": 24.0,
        "$require_import": true
    },
    "trigMax": {
        "description": "Maximum expected voltage level for the trigger signal.",
        "type": "number",
        "default": 5.0,
        "minimum": -24.0,
        "maximum": 24.0,
        "$require_import": true
    },
    "trigHysteresis": {
        "type":"number",
        "description": "Percentage hysteresis to apply to the tach trigger level",
        "default":2.5,
        "minimum": 0.0,
        "maximum": 99.0,
        "$require_import": true
    },
    "trigInverted": {
        "type":"boolean",
        "description": "If inverted, tach signal will be treated as having a low active level",
        "default": false,
        "$require_import": true
    },
    "trigDebounceSource": {
        "type": "string",
        "description": "The method to use for debounce calculations. Can be any of the following: \"Max RPM\", \"Max Frequency\", \"Min Period\", \"Debounce Duration\"",
        "default": "Debounce Duration",
        "$require_import": true
    },
    "trigDebounceMaxFrequency": {
        "type": "number",
        "description": "Maximum frequency of the tach signal for debounce calculations when using \"Max Frequency\" debounce method",
        "default": 0.0,
        "minimum": 0.0,
        "maximum": 50000.0,
        "$require_import": true
    },
    "trigDebounceMaxRPM": {
        "type": "number",
        "description": "Maximum RPM for debounce calculations when using \"Max RPM\" debounce method",
        "default": 0.0,
        "minimum": 0.0,
        "maximum": 400000.0,
        "$require_import": true
    },
    "trigDebounceMaxRpmPPR": {
        "type": "number",
        "description": "Pulses per Revolution to use in debounce calculations when using \"Max RPM\" debounce method",
        "default": 1.0,
        "minimum": 1.0,
        "maximum": 360.0,
        "$require_import": true
    },
    "trigDebounceMinPeriod_ms": {
        "type": "number",
        "description": "Minimum period in milliseconds for debounce calculations when using \"Min Period\" debounce method",
        "default": 0.0,
        "minimum": 0.0,
        "maximum": 60000.0,
        "$require_import": true
    },
    "trigDebounceDirect_ms": {
        "type": "number",
        "description": "Direct debounce duration in milliseconds to apply to the tach signal when using \"Debounce Duration\" debounce method",
        "default": 0.0,
        "minimum": 0.0,
        "maximum": 6000.0,
        "$require_import": true
    }
```