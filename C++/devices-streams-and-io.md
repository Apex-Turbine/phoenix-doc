# C++ Devices, Live Streams, File I/O & Databases

In addition to running full `GraphJson` pipelines, C++20 callers can instantiate and drive Phoenix **Devices**, **StreamReceivers/Transmitters**, **FileReaders/Writers**, and **Database Connections/Readers/Writers** directly through PhoenixCore interfaces and plugin library managers.

## 1. Hardware Devices (`Device` & `DeviceLibrary`)

All physical acquisition and output drivers inherit `Device` (`PhoenixCore/Device/Device.hpp`), which implements the Non-Virtual Interface (NVI) pattern around hardware-specific protected `p*` methods.

### Loading & Driving a `Device`

1. Load driver plugins via `getDeviceLibraryManager()->addPath("bin/devices")` (`GetDevices`).
2. Create an instance from the matching `DeviceAdapter` (`createDevice()`).
3. Run the standard lifecycle:

```cpp
// 1. Connect to chassis/endpoint
device->connect(uri, options);

// 2. Initialize & automatically execute hardware serial licensing checkouts
device->initialize();

// 3. Configure streams & settings tree (System -> Module -> Channel)
auto tree = device->getDeviceStructure();
device->applySettings(tree); // or applyItemSettings(...) / applyItemSetting(...)

// 4. Register telemetry callback & start streaming
device->setDataCallback([](const Message& msg) {
    // Handle incoming channel telemetry (srcIdx, streamIdx, nanosecond timestamps)
});
device->startStreaming();

// ... run acquisition ...

// 5. Stop streaming and release hardware license checkouts
device->stopStreaming();
device->uninitialize();
device->disconnect();
```

Additional `Device` capabilities:
- **Discovery**: `device->discover(timeoutMs)` scans LAN/buses returning JSON objects (`name`, `host`, `port`, `deviceType`, `typeUUID`, `protocol`, `dataPort`, `requiresAuth`, `discoveredVia`, `metadata`).
- **Health & Output Binding**: `getHealthStreams()`, `setOutputStreams(bindings)`, `write(msg)`.
- **Synchronization & Commands**: `getDeviceTime()`, `setDeviceTime(ns)`, `getUtcOffset()`, `enablePtp(bool)`, `setSyncMode(mode)`, `getAvailableCommands(itemId)`, `executeCommand(itemId, cmd)`.

See [Device Subsystem](../PhoenixCore/Device.md) for supported vendor adapters.

## 2. Live Network Streams (`StreamLibrary`, `StreamReceiver`, `StreamTransmitter`)

Load network transport plugins (`FILE` / `STREAM`) via `getStreamLibraryManager()->addPath("bin/stream")` (`STREAM_LIBRARY_SYMBOL "GetStreams"`). Shipped adapters include:
- **`DXPNetReceiver` / `DXPNetTransmitter`**: Native Phoenix ZeroMQ publisher/subscriber transport backed by `ZMQContext` (`Utilities/ZMQ/ZmqContext.hpp`).
- **`DSNetReceiver`**: Bridge client ingesting live **APEX DS (DS 3.x)** network telemetry directly into Phoenix `Message` pipelines.

See [Stream Subsystem](../PhoenixCore/Stream.md) for receiver callback interfaces (`StreamReceiverCallback`).

## 3. File I/O (`FileLibrary`, `FileReader`, `FileWriter`)

Load file format plugins via `getFileLibraryManager()->addPath("bin/file")` (`FILE_LIBRARY_SYMBOL "GetFiles"`):
- **Shipped Formats**:
  - `RwxReader` / `RWXWriter` (`.rwx` extended raw archives)
  - `DatxReader` / `DatxWriter` (`.datx` indexed block archives)
- **APEX-H5 Note**: `APEX-H5` output configuration is documented in [`readme/outputs/apex-h5.md`](../readme/outputs/apex-h5.md) and [`Components/Outputs/HDF5Output.md`](../Components/Outputs/HDF5Output.md). At the `FileLibrary` C++ plugin level, `File/HDF5` headers are currently unimplemented stubs commented out in `File/CMakeLists.txt`.

See [File Subsystem](../PhoenixCore/File.md) for `FileReaderCallback` and random-access query signatures.

## 4. Database I/O (`DatabaseLibrary`, `SQLite` & `PostgreSQL`)

Load database drivers via `getDatabaseLibraryManager()->addPath("bin/database")` (`DATABASE_LIBRARY_SYMBOL "GetDatabases"`):
- **SQLite**: `DXDBReader` / `DXDBWriter` (embedded `.dxdb` / `.sqlite` recordings)
- **PostgreSQL**: `PostgreSQL Database Reader` / `PsqlDBWriter` (centralized historian storage)

### Low-Level SQL Queries (`PhoenixDBConnection` & `DBQuery`)

For direct parameterized queries and binary blob retrieval (`Message` payloads, JSON metadata tables), use `PhoenixDBConnection` and `DBQuery` (verified in `comprehensive_integration_test.cxx`):

```cpp
// Prepare and execute query via DBQuery
// while (query->step()) {
//     int64_t id      = query->columnInteger(0);
//     double  val     = query->columnDouble(1);
//     std::string txt = query->columnText(2);
//     // BLOB payload: query->columnBlob(3)
// }
```

See [Database Subsystem](../PhoenixCore/Database.md) for `PhoenixDBReader`, `PhoenixDBWriter`, and schema versioning helpers (`DBSchemaVersion`, `DBMigration`).
