# Writing Custom C++ Component and Device Plugins

Extend Phoenix dynamically without recompiling core libraries by shipping shared libraries (`.dll` / `.so`) that implement **`Component`** or **`Device`** and export the standardized library entry point for `ComponentLibraryManager` or `DeviceLibraryManager`.

---

## 1. Custom Component Plugin (`GetComponents`)

### Implementation Checklist
1. Inherit from `Component` (`PhoenixCore/Component/Component.hpp`).
2. Provide static metadata (`static const componentInfo_t& getInfo()`) and factory (`static std::shared_ptr<Component> createComponent(const std::string& name)`) if using the `PhoenixComponentAdapter<T>` / `ComponentLibraryItem<T>()` wrapper pattern (`Components/PhoenixComponentAdapter.hpp`), or subclass `ComponentAdapter` (`PhoenixCore/Component/ComponentLibrary.hpp`) directly.
3. Implement `process(const Message& msg)` and `info()`, plus any needed lifecycle overrides (`setup`, `import`, `start`, `stop`, `dataReset`, `reset`, `commands`, `requests`, `command`, `request`).
4. Export `extern "C"` **`GetComponents()`** (`COMPONENT_LIBRARY_SYMBOL`) returning a `ComponentLibrary*` (`PhoenixLibrary<ComponentAdapter>*`).

### Minimal Component Plugin Skeleton

```cpp
#include <PhoenixCore/Component/Component.hpp>
#include <PhoenixCore/Component/ComponentLibrary.hpp>
#include <boost/uuid/string_generator.hpp>
#include <boost/uuid/uuid_io.hpp>

class CustomGainComponent : public Component {
public:
    CustomGainComponent(const std::string& name)
        : Component(name, "custom-gain-uuid") {}

    const componentInfo_t& info() override {
        return getInfo();
    }

    static const componentInfo_t& getInfo() {
        static componentInfo_t ci = [] {
            componentInfo_t info;
            // Populate info.m_name, m_componentUuid, m_vendor, m_componentClass,
            // m_componentClassType, m_supportedInputs, etc.
            return info;
        }();
        return ci;
    }

    static std::shared_ptr<Component> createComponent(const std::string& name) {
        return std::make_shared<CustomGainComponent>(name);
    }

    void process(const Message& msg) override {
        // Transform incoming Message buffer and forward via sendMsg(outMsg)
        sendMsg(msg);
    }
};

class CustomGainAdapter : public ComponentAdapter {
public:
    std::shared_ptr<Component> create(const std::string name) const override {
        return CustomGainComponent::createComponent(name);
    }
    std::string getName() const override { return CustomGainComponent::getInfo().m_name; }
    std::string getUUID() const override {
        return boost::uuids::to_string(CustomGainComponent::getInfo().m_componentUuid);
    }
    JSON getInfo() const override {
        JSON j;
        to_json(j, CustomGainComponent::getInfo());
        return j;
    }
};

#ifdef _WIN32
#define PLUGIN_EXPORT extern "C" __declspec(dllexport)
#else
#define PLUGIN_EXPORT extern "C"
#endif

PLUGIN_EXPORT ComponentLibrary* GetComponents() {
    static ComponentLibrary* lib = nullptr;
    if (!lib) {
        std::vector<std::shared_ptr<ComponentAdapter>> adapters;
        adapters.push_back(std::make_shared<CustomGainAdapter>());
        lib = new ComponentLibrary("CustomComponents", adapters);
    }
    return lib;
}
```

### Registering and Testing at Runtime

```cpp
auto mgr = getComponentLibraryManager();
mgr->addPath("/path/to/custom/component/plugins");

for (auto* lib : mgr->getLibraries()) {
    for (const auto& adapter : lib->getAdapters()) {
        auto comp = adapter->create("instance-1");
        std::string report = comp->selfTest();
    }
}
```

---

## 2. Custom Hardware Device Plugin (`GetDevices`)

### Implementation Checklist
1. Inherit from `Device` (`PhoenixCore/Device/Device.hpp`), implementing the protected NVI hooks:
   - Connection & Lifecycle: `pConnect`, `pDisconnect`, optional `pInitialize`, `pUninitialize`
   - Hardware Tree & Settings: `pGetDeviceStructure`, `pApplySettings`, optional `pApplyItemSettings`
   - Streams & Streaming: `pGetStreams`, `pStartStreaming`, `pStopStreaming`, `pWrite`, `pSetOutputStreams`, optional `pGetHealthStreams`, `pDiscover`, `pSetEnabledStreams`
   - Identity & Licensing: `pGetDeviceInfo`, optional `pGetHardwareIdentifiers`, `pGetHardwareIdentifierForStream`
   - Time & Commands: `pGetDeviceTime`, `pSetDeviceTime`, `pGetUtcOffset`, `pSetUtcOffset`, `pGetSyncInfo`, `pEnablePtp`, `pGetAvailableCommands`, `pExecuteCommand`
2. Subclass `DeviceAdapter` (`createDevice()`, `getDeviceName()`, `getDeviceUUID()`, `getDeviceInfo()`).
3. Export `extern "C"` **`GetDevices()`** returning `PhoenixLibrary<DeviceAdapter>*` (`DeviceLibrary*`, see `Device/ApexDeviceLibrary.cxx`).

### Minimal Device Plugin Export (`Device/ApexDeviceLibrary.cxx` pattern)

```cpp
#include <PhoenixCore/Device/DeviceLibrary.hpp>
#include <boost/uuid/uuid_io.hpp>

class MyCustomDevice : public Device {
    // Implement required protected p* overrides
};

class MyCustomDeviceAdapter : public DeviceAdapter {
public:
    std::shared_ptr<Device> createDevice() override {
        return std::make_shared<MyCustomDevice>();
    }
    std::string getDeviceName() const override {
        return MyCustomDevice().getDeviceInfo().name;
    }
    std::string getDeviceUUID() const override {
        return boost::uuids::to_string(MyCustomDevice().getDeviceInfo().uuid);
    }
    deviceInfo_t getDeviceInfo() const override {
        return MyCustomDevice().getDeviceInfo();
    }
};

#ifdef _WIN32
#define DEVICE_EXPORT extern "C" __declspec(dllexport)
#else
#define DEVICE_EXPORT extern "C"
#endif

DEVICE_EXPORT PhoenixLibrary<DeviceAdapter>* GetDevices() {
    static std::vector<std::shared_ptr<DeviceAdapter>> adapters = {
        std::make_shared<MyCustomDeviceAdapter>()
    };
    static DeviceLibrary lib("CustomDeviceLibrary", adapters);
    return &lib;
}
```

### CMake Plugin Build Target

Compile plugins as C++20 `SHARED` libraries linked against `Phoenix::PhoenixCore`:

```cmake
add_library(my_phoenix_plugin SHARED
    CustomGainComponent.cpp
)
set_target_properties(my_phoenix_plugin PROPERTIES CXX_STANDARD 20 CXX_STANDARD_REQUIRED ON)
target_link_libraries(my_phoenix_plugin PRIVATE Phoenix::PhoenixCore)
```

Place the compiled `.dll` / `.so` inside a directory registered with `getComponentLibraryManager()->addPath(...)` or `getDeviceLibraryManager()->addPath(...)`.
