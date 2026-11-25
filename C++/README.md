# Phoenix C++20 SDK Overview

Build native C++20 applications and custom plugin libraries directly against **`PhoenixCore`** (`Phoenix::PhoenixCore`) and Phoenix's runtime plugin managers.

## Requirements & CMake Integration

- **C++ Standard**: C++20 (`CMAKE_CXX_STANDARD 20` in `phoenix/CMakeLists.txt`).
- **Core Library**: Link against `PhoenixCore` (`Phoenix::PhoenixCore`), exporting symbols via `PHOENIXCORE_API_SYMBOL`.
- **Dynamic Plugin Loading**: Instead of linking every driver, database backend, or processor directly, query Phoenix's singleton library managers and point them at plugin directories (`bin/`, `bin/components/`, `bin/database/`, `bin/file/`, `bin/stream/`, `bin/virtual/`):

```cmake
cmake_minimum_required(VERSION 3.20)
project(phoenix_custom_app LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Phoenix REQUIRED) # Provides Phoenix::PhoenixCore

add_executable(my_pipeline main.cxx)
target_link_libraries(my_pipeline PRIVATE Phoenix::PhoenixCore)
```

## Runtime Plugin Architecture (`PhoenixLibraryManager`)

Every subsystem uses `PhoenixLibraryManager<T>` (`PhoenixCore/Utilities/PhoenixLibrary.hpp` & `LibraryMacros.hpp`) to dynamically load `.dll` (Windows) or `.so` (Linux) plugin modules via `addPath(dir)` and resolve their exported C factory symbol:

| Manager Accessor | Exported Symbol | Adapter Type | Managed Capability |
| --- | --- | --- | --- |
| `getComponentLibraryManager()` | `GetComponents` (`COMPONENT_LIBRARY_SYMBOL`) | `ComponentAdapter` | Data sources, DSP processors, outputs, viewers |
| `getDeviceLibraryManager()` | `GetDevices` | `DeviceAdapter` | Hardware acquisition & output drivers (`Device`) |
| `getFileLibraryManager()` | `GetFiles` (`FILE_LIBRARY_SYMBOL`) | `FileAdapter` | `.rwx` and `.datx` file readers/writers |
| `getDatabaseLibraryManager()` | `GetDatabases` (`DATABASE_LIBRARY_SYMBOL`) | `PhoenixDBAdapter` | `SQLite` and `PostgreSQL` readers/writers |
| `getStreamLibraryManager()` | `GetStreams` (`STREAM_LIBRARY_SYMBOL`) | `StreamAdapter` | Live `DXPNet` ZMQ & `DSNet` DS 3.x streams |
| Video & Virtual managers | `GetVideoIO`, `GetVirtuals` | `VideoAdapter` / Virtual adapters | Video inputs and VTK virtual sensor models |

## Application Startup Sequence

A standard standalone C++20 application initializes subsystems in this order:

1. **Initialize Event Diagnostics**: Create a shared `EventManager` and attach handlers (`PrintEventHandler` or custom `EventHandler`).
2. **Initialize Licensing (`PhoenixLic`)**: Configure app identity (`setAppInfo`), connect to ApexLM (`connect`), and check out required feature counts or hardware serials before creating licensed components/devices ([Licensing Guide](licensing.md)).
3. **Load Plugin Directories**: Call `addPath(...)` on `getComponentLibraryManager()`, `getDeviceLibraryManager()`, `getFileLibraryManager()`, `getDatabaseLibraryManager()`, and/or `getStreamLibraryManager()`.
4. **Run Pipeline or Direct I/O**: Execute a `GraphJson` / `TableJson` topology via `OnlineComponentManager` or `OfflineQueryComponentManager` ([Pipelines](pipeline-and-components.md)), or open `Device` / `FileReader` / `PhoenixDBConnection` / `StreamReceiver` directly ([Devices, Streams & I/O](devices-streams-and-io.md)).
5. **Graceful Teardown**: Stop managers/streams (`stop()` drains queued worker tasks before destroying components) and check in/disconnect licenses.

## Threading & Callback Lifecycle

- **Subscription Queues (`ComponentSubscriptionHandler`)**: Each downstream component owns a thread-pool-scheduled queue (`PhoenixThreadPool`). Queue backlog is monitored every `3.0s` (`MONITOR_INTERVAL_SEC`) with overrun alerts triggered when `OVERRUN_RATIO > 1.5` and backlog exceeds `2000` (`OVERRUN_MIN_BACKLOG`) across `2` consecutive checks (`REQUIRED_CONSECUTIVE_CHECKS`).
- **Drain-Safe Stop**: `ComponentSubscriptionHandler::working()` checks both `m_working` and `m_workerScheduled` while holding `m_mtx`, ensuring `ComponentManager::stop()` cleanly drains active worker tasks.
- **User Data Taps**: Attach subscriber callbacks via `ComponentManager` / `ComponentCallbackItem` or device callbacks via `Device::setDataCallback(DataCallback_t)` without holding mutexes across blocking disk/network calls.
