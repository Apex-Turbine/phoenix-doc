# PhoenixJson Helper Utilities

## Overview

The `PhoenixJson` module (`PhoenixCore/Utilities/PhoenixJson.hpp`) provides robust, exception-safe helper functions and extensions over the `nlohmann::json` C++ library.

In real-time telemetry processing, configuration parameters and metadata often have missing keys, type coercions, or null values. Standard `nlohmann::json::get<T>()` or `operator[]` operations throw `std::out_of_range` or `nlohmann::json::type_error` exceptions when encountering missing or mismatched data. `PhoenixJson` eliminates these crashes by providing type-safe extraction with default fallbacks.

```mermaid
graph TD
    A["Raw JSON Object"] --> B{"PhoenixJson::getDouble(json['gain'], 1.0)"}
    B -->|Key Exists & Valid Number| C["Returns Parsed Double"]
    B -->|Key Missing or Null| D["Returns Default (1.0)"]
    B -->|Type Mismatch (e.g. String '1.5')| E["Attempts Safe Coercion or Returns Default"]
```

---

## C++ API Reference

All functions reside in the `PhoenixJson` namespace:

```cpp
#include <PhoenixCore/Utilities/PhoenixJson.hpp>
```

| Function Signature | Description | Default If Missing / Invalid |
|---|---|---|
| `getString(const JSON& j, const std::string& dflt = "")` | Extracts a string value. Safely converts numbers/booleans to strings. | `dflt` |
| `getDouble(const JSON& j, double dflt = 0.0)` | Extracts a double-precision float. | `dflt` |
| `getFloat(const JSON& j, float dflt = 0.0f)` | Extracts a single-precision float. | `dflt` |
| `getInt(const JSON& j, int32_t dflt = 0)` | Extracts a 32-bit signed integer. | `dflt` |
| `getUInt(const JSON& j, uint32_t dflt = 0)` | Extracts a 32-bit unsigned integer. | `dflt` |
| `getInt64(const JSON& j, int64_t dflt = 0)` | Extracts a 64-bit signed integer. | `dflt` |
| `getUInt64(const JSON& j, uint64_t dflt = 0)` | Extracts a 64-bit unsigned integer. | `dflt` |
| `getBool(const JSON& j, bool dflt = false)` | Extracts a boolean value. | `dflt` |
| `getChild(JSON& j, const std::string& key)` | Safely retrieves or creates a child object. | Empty object `{}` |
| `isArray(const JSON& j, const std::string& key)` | Checks if a key exists and is a JSON array. | `false` |
| `getJson(const std::string& jsonStr)` | Safely parses a JSON string without throwing. | Empty JSON `{}` |
| `getJsonString(const JSON& j)` | Serializes JSON to a compact string. | `"{}"` |

---

## C++ Code Examples

### 1. Safely Extracting Component Configuration

```cpp
#include <PhoenixCore/Utilities/PhoenixJson.hpp>
#include <iostream>

void configureComponent(const JSON& settings)
{
    // Extract parameters with safe fallbacks
    double gain = PhoenixJson::getDouble(settings["gain"], 1.0);
    double offset = PhoenixJson::getDouble(settings["offset"], 0.0);
    int sampleRate = PhoenixJson::getInt(settings["sample_rate"], 1000);
    std::string channelName = PhoenixJson::getString(settings["channel_name"], "DefaultChannel");
    bool isEnabled = PhoenixJson::getBool(settings["enabled"], true);

    std::cout << "Configured " << channelName 
              << ": Rate=" << sampleRate << "Hz, Gain=" << gain << ", Offset=" << offset
              << ", Enabled=" << (isEnabled ? "Yes" : "No") << "\n";
}
```

### 2. Safely Navigating Nested JSON Objects

```cpp
void inspectStreamMetadata(const JSON& streamJson)
{
    // Checks if classifiers array exists
    if (PhoenixJson::isArray(streamJson, "classifiers")) {
        for (const auto& tag : streamJson["classifiers"]) {
            std::cout << "Classifier Tag: " << tag.get<std::string>() << "\n";
        }
    }
}
```

---

## Python Usage (`PhoenixPy`)

In Python via `phoenixpy.phoenixJson`, `PhoenixJson` helpers provide null-safe data extraction from JSON structures:

```python
from phoenixpy import phoenixJson

def parse_settings_in_python(settings_dict):
    # Safe extraction with defaults
    gain = phoenixJson.PhoenixJson.getDouble(settings_dict.get("gain"), 1.0)
    offset = phoenixJson.PhoenixJson.getDouble(settings_dict.get("offset"), 0.0)
    sample_rate = phoenixJson.PhoenixJson.getInt(settings_dict.get("sample_rate"), 1000)
    channel = phoenixJson.PhoenixJson.getString(settings_dict.get("name"), "Unnamed")

    print(f"Loaded Channel {channel}: Rate={sample_rate} Hz, Gain={gain}, Offset={offset}")

if __name__ == "__main__":
    test_json = {"gain": 2.5, "name": "PressureSensor1"}
    parse_settings_in_python(test_json)
```
