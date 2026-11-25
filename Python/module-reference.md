# `phoenixpy` SWIG Module Reference

Organized index of the SWIG modules compiled into `phoenixpy` (packaged under `cp312` `abi3` with `-DSWIG_PYTHON_STRICT_BYTE_CHAR`).

## Licensing (`PhoenixLic/`)

| SWIG Module | Primary Exported Types / Symbols |
| --- | --- |
| `PhoenixLic` | `PhoenixLic` (`instance()`, `setAppInfo`, `connect`, `queryLicenseSeats`, `checkoutFeature`, `checkoutHardware`, `checkinFeature`, `checkinHardware`, `disconnect`) |
| `apexlmclient` | `ApexLmClient` (`DEFAULT_APP_ID`), `HardwareLicenseRequest` |

## Components & Pipelines (`Component/`)

| SWIG Module | Primary Exported Types / Symbols |
| --- | --- |
| `Component` | `Component` base class, `stream_info_t`, `LIC_*` macros, `COMP_*` state macros |
| `ComponentInfo` | `componentInfo_t`, `ComponentClass`, `ComponentClassType`, `ComponentAppDomain`, `componentSupportedInputs_t` |
| `ComponentLibrary` | `ComponentAdapter`, `ComponentLibrary`, `ComponentLibraryManager`, `getComponentLibraryManager()` |
| `ComponentManager` | `ComponentManager` graph execution base |
| `OnlineComponentManager` | `OnlineComponentManager` live streaming pipeline runner |
| `OfflineQueryComponentManager` | `OfflineQueryComponentManager` historical replay runner |
| `ComponentRemoteManager` | `ComponentRemoteManager` ZMQ remote pipeline controller |
| `ComponentCallback` | `ComponentCallbackItem` director callback interface |
| `ComponentSubscriptionHandler` | `ComponentSubscriptionHandler` queue & overrun monitor (`enable`, `purge`, `working`, `getAverageIngestionRate`, `getAverageOutputRate`) |
| `StreamTableManager` | `StreamTableManager` (`buildGraphFromTable`) |

## Hardware Devices (`Device/`)

| SWIG Module | Primary Exported Types / Symbols |
| --- | --- |
| `Device` | `Device` NVI hardware interface (`connect`, `initialize`, `applySettings`, `startStreaming`, `stopStreaming`, `discover`, `write`) |
| `DeviceInfo` | `deviceInfo_t`, `DeviceIoType`, `DeviceSyncType` |
| `DeviceLibrary` | `DeviceAdapter`, `DeviceLibrary`, `DeviceLibraryManager`, `getDeviceLibraryManager()` |

## File & Database I/O (`File/`, `Database/`)

| SWIG Module | Primary Exported Types / Symbols |
| --- | --- |
| `File`, `FileInfo`, `FileLibrary`, `FileReader`, `FileReaderCallback`, `FileWriter` | `FileAdapter`, `FileLibraryManager`, `getFileLibraryManager()`, `FileReader`, `FileWriter`, `FileReaderCallback` |
| `Database`, `DatabaseInfo`, `DatabaseLibrary`, `DBQuery`, `PhoenixDBConnection`, `PhoenixDBReader`, `PhoenixDBReaderCallback`, `PhoenixDBWriter` | `PhoenixDBAdapter`, `DatabaseLibraryManager`, `getDatabaseLibraryManager()`, `PhoenixDBConnection`, `DBQuery` (`execute`, `step`, `columnInteger`, `columnDouble`, `columnText`, `columnBlob`), `PhoenixDBReader`, `PhoenixDBWriter`, `PhoenixDBReaderCallback` |

## Streaming (`Stream/`)

| SWIG Module | Primary Exported Types / Symbols |
| --- | --- |
| `Stream`, `StreamInfo`, `StreamLibrary`, `StreamReceiver`, `StreamReceiverCallback`, `StreamTransmitter` | `StreamAdapter`, `StreamLibraryManager`, `getStreamLibraryManager()`, `StreamReceiver`, `StreamTransmitter`, `StreamReceiverCallback` |

## Core Data Types, Time & Limits (`DataTypes/`)

| Group | SWIG Modules |
| --- | --- |
| **Core Envelopes & Config** | `DataTypes`, `Message`, `Sample`, `GraphJson`, `TableJson`, `Units`, `EventInfo`, `RecordInfo`, `PhoenixComm`, `Point2D`, `Point3D`, `SolutionFitData`, `StrainStress`, `MonitoredLimitsStruct` |
| **Precision Timing** | `CommonTimePoint`, `Constants`, `GoNoGo`, `PhoenixIRIG`, `PhoenixInternalTimeRef`, `PhoenixPTP`, `PhoenixTime`, `PhoenixTimeAdapter`, `TimecodeReference` |
| **Limit Envelopes** | `LimitsFileCSV`, `LimitsFileCommon`, `LimitsFileJson`, `LimitsJsonOperations`, `InterpolationJson`, `ModalLimitParameters`, `OrderLimitParameters`, `StaticLimitParameters` |

## Utilities (`Utilities/`)

| Group | SWIG Modules |
| --- | --- |
| **Diagnostics & JSON** | `EventManager`, `phoenixJson`, `version`, `Utils`, `FileUtils`, `PhoenixStringUtils`, `PhoenixUUID`, `NamingConventionHelpers`, `CryptoUtils`, `Automation`, `Condition` |
| **Math, FFT & Signal Processing** | `Math`, `Vector`, `VectorFit`, `VectorTemplates`, `FloatingPointUtils`, `Interpolation` (`Interpolator`, `LinearInterpolator`, `NearestNeighborsInterpolator`), `FFTMessage`, `Signals`, `ModalCharacteristics`, `TachUtils`, `TriggerConsumerStreams`, `TriggerUtils`, `TriggerWindow`, `Reblocker`, `WSBUtils`, `IRIGProcessor` |
| **Concurrency, Libraries & ZMQ** | `PhoenixThreadPool`, `PhoenixLibrary`, `ZmqContext`, `ZmqClient`, `ZmqServer` |

## Video, Digital Twin & Testing (`Video/`, `Virtual/`, `Testing/`)

| Group | SWIG Modules |
| --- | --- |
| **Video Capture** | `VideoFrameCallback`, `VideoInfo`, `VideoInput`, `VideoLibrary` |
| **Virtual Sensors & FEA Digital Twin** | `VirtualLibrary`, `VirtualModel`, `VirtualModelLoader`, `VirtualModelAlgorithm`, `VirtualModelRange`, `VirtualController`, `VirtualControllerSource`, `VirtualInfo`, `VirtualSensor`, `VirtualSensorInterpolator`, `VirtualSensorManager`, `NormalizationFilter`, `SolutionFilter`, `StatAccumulationFilter`, `MaterialProperties`, `DisplacementSensor`, `PressureSensor`, `StrainGage`, `TemperatureSensor` |
| **Testing** | `TestDataSource` |

For detailed C++ behaviors of these subsystems, browse the [PhoenixCore Architecture Guides](../PhoenixCore/PhoenixCore.md).
