# GraphJson Documentation

## Overview

GraphJson is a hierarchical JSON-based data structure that represents a computational data pipeline. It defines the topology and configuration of data sources, streams, processing rules, and automation logic. The structure is designed for persistence, transmission, and interpretation by the ComponentManager to construct and execute data pipelines.

### Key Concepts

- **Data Source**: Represents a producer (device/file), processor, or consumer (file writer/network publisher) of data
- **Stream**: Individual data channel with type information, units, limits, and optional dependencies on other streams
- **Condition**: Logical predicate that evaluates stream data to trigger automations
- **Automation**: Action rule triggered when condition(s) are met

---

## JSON Structure

### Root GraphJson Structure

```json
{
  "name": "unique-graph-identifier",
  "version": "1.0",
  "mode": "analysis",
  "date": "2026-03-03",
  "properties": {
    "custom_property": "value"
  },
  "metadata": {
    "author": "System",
    "description": "Pipeline description"
  },
  "datasources": [
    { "SourceJson object" }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Unique identifier for the graph (typically UUID, auto-generated if not provided) |
| `version` | string | Schema version (default: "1.0") |
| `mode` | string | Operational mode (e.g., "analysis", "monitoring", "streaming") |
| `date` | string | Creation/modification date |
| `properties` | object | Arbitrary graph-level properties |
| `metadata` | object | Graph metadata (author, description, etc.) |
| `datasources` | array | Array of SourceJson objects representing data sources in the pipeline |

#### Example

```json
{
  "name": "550e8400-e29b-41d4-a716-446655440000",
  "version": "1.0",
  "mode": "analysis",
  "date": "2026-03-03T10:30:00Z",
  "metadata": {
    "author": "operator@example.com",
    "description": "Multi-sensor data aggregation and alerting pipeline"
  },
  "datasources": []
}
```

---

### SourceJson Structure

A SourceJson represents an instance of a component in the pipeline. Each SourceJson specifies which component implementation to load and how to configure it.

```json
{
  "name": "550e8400-e29b-41d4-a716-446655440001",
  "logicalName": "Primary Sensor Array",
  "type": "39d09444-da10-426c-a8d1-40e605008c15",
  "enabled": true,
  "metadata": {
    "manufacturer": "Acme Corp",
    "model": "AS-2000",
    "serial": "SN12345"
  },
  "settings": {
    "connection_string": "COM3:115200",
    "timeout_ms": 5000,
    "streams": [
      { "StreamJson objects" }
    ],
    "automations": [
      { "AutomationJson objects" }
    ],
    "conditions": [
      { "ConditionJson objects" }
    ]
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Unique instance identifier (UUID convention recommended) |
| `logicalName` | string | Human-readable display name |
| `type` | string | Component type UUID - specifies which component implementation to load (see [Component.md](Component.md) for component definition) |
| `enabled` | boolean | Enable/disable this component instance |
| `metadata` | object | Component instance metadata (manufacturer, model, serial, etc.) |
| `settings` | object | Component configuration parameters; contains nested `streams`, `automations`, `conditions` arrays |

#### Naming Convention

- **`name`**: Should be a UUID (e.g., `550e8400-e29b-41d4-a716-446655440001`) to ensure uniqueness of the component instance in the graph
- **`logicalName`**: User-readable name for display in UIs (e.g., "Primary Sensor Array", "Data Filter", "File Output")
- **`type`**: UUID of the component implementation (e.g., `39d09444-da10-426c-a8d1-40e605008c15`)

#### Component Classes and Types

Component class and class_type information (such as "device", "file", "store", "dbms", "web", "processor", or output types) are defined in the component's **information JSON** object, external to GraphJson. These can be determined by:

1. Taking the `type` UUID from the SourceJson
2. Loading the component implementation with the ComponentManager or ComponentLibraryManager
3. Calling `component.info()` to retrieve the component information JSON
4. Reading the `class` (producer/processor/consumer) and `class_type` fields from the information JSON

For reference, see [Component.md](Component.md) for the complete list of component class types: device, file, store, dbms, web, processor, and various output types.

---

### StreamJson Structure

Streams represent individual data channels produced or consumed by a source. They contain metadata about the data's type, units, limits, and dependencies.

```json
{
  "name": "Temperature",
  "logicalName": "System Temperature",
  "type": "stream",
  "group": "Analog",
  "dtype": "number",
  "mtype": "entry",
  "enabled": true,
  "uniform": true,
  "continuous": true,
  "delta": 0.1,
  "units": [
    { "domain": "time", "unit": "second" },
    { "domain": "temperature", "unit": "celsius" }
  ],
  "sources": [
    {
      "sourcename": "550e8400-e29b-41d4-a716-446655440001",
      "streamname": "RawTemp",
      "streamlogicalname": "Raw Sensor Reading"
    }
  ],
  "limits": {
    "min": -40.0,
    "max": 125.0,
    "warn": 70,
    "alert": 90
  },
  "metadata": {
    "sensor_id": "TEMP_001",
    "calibration_date": "2026-01-15"
  },
  "minref": -40.0,
  "maxref": 125.0,
  "streamClass": 0,
  "classifiers": {
    "domain": ["thermal"],
    "importance": ["critical"]
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Stream identifier (unique within source) |
| `logicalName` | string | Human-readable display name |
| `type` | string | Stream type (typically "stream") |
| `group` | string | Stream grouping/category (e.g., "Analog", "Digital", "Status") |
| `dtype` | string | Data type: "number", "integer", "boolean", "complex", "string", "struct", "message" |
| `mtype` | string | Message structure: "entry", "vector", "matrix", "tensor", "point2d", "point3d", "video", "json", "bson" |
| `enabled` | boolean | Enable/disable stream |
| `uniform` | boolean | Whether stream has uniform (regular) sampling |
| `continuous` | boolean | Whether stream represents continuous data |
| `delta` | number | Sampling interval (for uniform streams); units defined by first unit domain |
| `units` | array | Physical units array; first entry is reference dimension (usually time) |
| `sources` | array | Data dependencies: array of `stream_srcitem_t` objects |
| `limits` | object | Warning/alert thresholds |
| `metadata` | object | Stream metadata (sensor info, calibration data, etc.) |
| `minref` | number | Reference minimum value |
| `maxref` | number | Reference maximum value |
| `streamClass` | integer | Classification code |
| `classifiers` | object | String classification tags organized by category |

#### Stream Origin and Dependencies (Empty Sources List)

- If `sources` array is **empty**: The source component **creates** this stream (producer of raw data)
  - Stream `name` follows convention: `<componentName>/<unique-stream-id>`
  - Example: `SensorDevice1/Temperature`, `FileReader1/Channel01`
  - The component's `name` is used as the `originSource` value
  - When the stream is first created, the component sets `originSource` to indicate the component that created it

- If `sources` array contains entries: This stream depends on data from other sources' streams (derived/processed)
  - Stream is created by merging, transforming, or processing other streams
  - The `sources` array shows all input dependencies with component names and stream identifiers
  - Stream `name` may include intermediate component path: `<originSource>/<intermediateComponent>/<unique-stream-id>`
  - Example: `SensorDevice1/FilterProcessor1/FilteredTemperature` (stream from SensorDevice1, after FilterProcessor1)

```cpp
// Example 1: Producer stream (empty sources) - component creates raw data
StreamJson rawTemperature;
rawTemperature.name() = "SensorDevice1/RawTemp";      // <componentName>/<streamId>
rawTemperature.originSource() = "SensorDevice1";       // Set origin to creator
rawTemperature.sources().clear();                      // Empty - this source creates this stream
source.streams().push_back(rawTemperature);

// Example 2: Processor stream (has sources) - depends on another stream
StreamJson filteredTemperature;
filteredTemperature.name() = "SensorDevice1/FilterProcessor1/FilteredTemp";  // Path from origin through processor
filteredTemperature.originSource() = "SensorDevice1";   // Original creator (even though processed)
stream_srcitem_t dependency;
dependency.srcname = "550e8400-e29b-41d4-a716-446655440001";  // Source UUID
dependency.streamname = "SensorDevice1/RawTemp";             // Full path of input stream
dependency.streamlogicalname = "Raw Sensor Reading";         // Logical name
filteredTemperature.sources().push_back(dependency);         // Has dependency - processor uses this
processorSource.streams().push_back(filteredTemperature);
```

#### Stream Naming Convention

- **`name`**: Path-format identifier combining origin component and unique stream identifier within that component
  - Format: `<originSource>/<uniqueStreamName>`
  - Examples: `SensorDevice1/Temperature`, `FilterProcessor1/FilteredVoltage`
  - Used programmatically to access streams
  - When streams are processed through multiple components and merged (e.g., two scaling operations merged at an FFT), the path extends: `<originSource>/<component1>/<component2>/<uniqueStreamName>`
  - The merge-point component is responsible for updating names to avoid conflicts
  - See [Component.md](Component.md#stream-naming-convention) for detailed naming rules

- **`logicalName`**: Human-readable display name (e.g., "Ambient Temperature", "System Pressure Drop")
  - Used in UIs and user-facing documentation
  - Can include spaces and special characters
  - Falls back to `name` if not explicitly set
  - Logical name should remain consistent even if the stream's physical name path updates during component merging

```json
{
  "name": "SensorDevice1/AmbientTemp",
  "logicalName": "Ambient Temperature Reading",
  "originSource": "SensorDevice1",
  "...": "..."
}
```

When a stream is processed through components and the same stream appears twice at a merge point:

```json
{
  "name": "SensorDevice1/PreprocessScaler1/AmbientTemp",
  "logicalName": "Ambient Temperature Reading",
  "originSource": "SensorDevice1",
  "...": "..."
}
```

```json
{
  "name": "SensorDevice1/PreprocessScaler2/AmbientTemp",
  "logicalName": "Ambient Temperature Reading",
  "originSource": "SensorDevice1",
  "...": "..."
}

#### stream_srcitem_t Structure

Represents a dependency on another stream:

```json
{
  "sourcename": "550e8400-e29b-41d4-a716-446655440001",
  "streamname": "SensorDevice1/Temperature",
  "streamlogicalname": "Ambient Temperature"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `sourcename` | string | UUID of the source component containing the stream |
| `streamname` | string | Stream identifier using path convention `<originSource>/<uniqueStreamName>` (or extended path if processed through intermediate components) |
| `streamlogicalname` | string | Logical name (optional, for reference/debugging) |

#### stream_limits Structure

Defines alarm/warning thresholds:

```json
{
  "min": -40.0,
  "max": 125.0,
  "warn": 70,
  "alert": 90
}
```

| Field | Type | Description |
|-------|------|-------------|
| `min` | number | Minimum acceptable data value |
| `max` | number | Maximum acceptable data value |
| `warn` | number | Warning threshold as percentage of range (0-100) |
| `alert` | number | Alert threshold as percentage of range (0-100) |

---

### ConditionJson Structure

Conditions define logical predicates that evaluate stream data to determine when to trigger automations.

```json
{
  "id": "cond-pressure-high-001",
  "display_name": "High Pressure Warning",
  "type": "limit_warn",
  "is_enabled": true,
  "arguments": [
    {
      "sourcename": "550e8400-e29b-41d4-a716-446655440001",
      "streamname": "Pressure",
      "streamlogicalname": "System Pressure"
    }
  ],
  "settings": {
    "inverted": false,
    "sustain": 2.0,
    "release": 1.0
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique condition identifier |
| `display_name` | string | Human-readable condition name |
| `type` | string | Condition type (see AutomationsLib section) |
| `is_enabled` | boolean | Enable/disable condition evaluation |
| `arguments` | array | Array of `stream_srcitem_t` objects being monitored |
| `settings` | object | Condition type-specific parameters |

#### Common Condition Types (from AutomationsLib)

See [AutomationsLib](#automationslib-reference) section for detailed implementation. Common types:

- **`limit_raw`**: Evaluate against raw limit interval (min/max)
- **`limit_warn`**: Evaluate against warning threshold interval
- **`limit_alert`**: Evaluate against alert threshold interval

#### Condition Settings

Standard settings recognized by LimitCondition:

```json
{
  "inverted": false,         // Negate the condition (true when normally would be false)
  "sustain": 2.0,            // Seconds condition must be true before changing state
  "release": 1.0             // Seconds condition must be false before changing state
}
```

---

### AutomationJson Structure

Automations are action rules triggered when specified conditions evaluate to true.

```json
{
  "id": "auto-pressure-alert-001",
  "display_name": "Pressure Alert Notification",
  "type": "notify",
  "is_enabled": true,
  "condition_connective": "OR",
  "conditions": [
    "cond-pressure-high-001",
    "cond-pressure-low-001"
  ],
  "settings": {
    "action": "send_notification",
    "recipients": ["operator@example.com"],
    "priority": "high"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique automation identifier |
| `display_name` | string | Human-readable automation name |
| `type` | string | Automation action type (e.g., "notify", "execute", "log") |
| `is_enabled` | boolean | Enable/disable automation execution |
| `condition_connective` | string | Logic operator: `"AND"` (all must be true) or `"OR"` (any must be true) |
| `conditions` | array | Array of condition IDs to evaluate |
| `settings` | object | Automation type-specific parameters |

#### Condition Connective Logic

```json
{
  "conditions": ["cond1", "cond2", "cond3"],
  "condition_connective": "AND"
}
```

- **`AND`**: Automation triggers only when **all** conditions are true
- **`OR`**: Automation triggers when **any** condition is true

---

## JSON Serialization and Deserialization

### Overview

GraphJson provides friend functions for nlohmann::json integration:

```cpp
friend PHOENIXCORE_API_SYMBOL void to_json(JSON& j, const GraphJson& p);
friend PHOENIXCORE_API_SYMBOL void from_json(const JSON& j, GraphJson& p);
```

### Serialization (C++ to JSON)

```cpp
GraphJson graph;
graph.name() = "550e8400-e29b-41d4-a716-446655440000";
// ... populate graph ...

// Serialize to JSON
JSON jsonObject;
to_json(jsonObject, graph);

// Convert to string
std::string jsonString = jsonObject.dump(2);  // Pretty print
```

### Deserialization (JSON to C++)

```cpp
// Parse JSON string
JSON jsonObject = JSON::parse(jsonString);

// Deserialize to GraphJson
GraphJson graph;
from_json(jsonObject, graph);

// Access loaded data
std::cout << "Graph: " << graph.name() << std::endl;
std::cout << "Sources: " << graph.sources().size() << std::endl;
```

### Serialization Details

#### StreamJson Serialization

```cpp
void to_json(JSON& j, const GraphJson::StreamJson& p)
{
  // Merge settings fields into root
  for (auto& [k, v] : p.m_settings.items()) {
    j[k] = v;
  }
  
  // Add stream fields
  j["name"] = p.m_name;
  j["logicalName"] = p.m_logicalName;
  j["type"] = p.m_type;
  j["dtype"] = p.m_dataType;          // Data type
  j["mtype"] = p.m_messageType;       // Message type
  j["delta"] = p.m_delta;              // Sampling interval
  j["continuous"] = p.m_isContinuous;
  j["uniform"] = p.m_isUniform;
  j["units"] = ...;                    // Unit objects
  j["sources"] = p.m_sources;          // Dependencies
  j["limits"] = p.m_limits;            // Limit thresholds
  j["metadata"] = p.m_metadata;
  // ... more fields
}
```

**Key behaviors:**
- Settings are merged into the stream JSON at the root level
- Backward compatibility: stores both `dtype`/`mtype` and `typecode`
- Units converted through `Unit::toJSON()`

#### SourceJson Serialization

```cpp
void to_json(JSON& j, const GraphJson::SourceJson& p)
{
  // Add source fields
  j["name"] = p.m_name;
  j["logicalName"] = p.m_logicalName;
  j["type"] = p.m_type;
  j["enabled"] = p.m_enabled;
  
  // Put streams, conditions, automations in settings
  j["settings"]["streams"] = p.m_streams;
  j["settings"]["automations"] = p.m_automations;
  j["settings"]["conditions"] = p.m_conditions;
  
  // Add other settings
  for (auto& [k, v] : p.m_settings.items()) {
    j["settings"][k] = v;
  }
  
  j["metadata"] = p.m_metadata;
}
```

**Key behaviors:**
- Streams, conditions, and automations nested under `settings`
- Other configuration stored in `settings` object
- Properties merged at source root

#### GraphJson (Root) Serialization

```cpp
void to_json(JSON& j, const GraphJson& p)
{
  j["name"] = p.m_name;
  j["version"] = p.m_version;
  j["mode"] = p.m_mode;
  j["date"] = p.m_date;
  j["properties"] = p.m_properties;
  j["metadata"] = p.m_metadata;
  j["datasources"] = p.m_sources;    // Array of SourceJson
}
```

### Deserialization Details

#### Key Observations from from_json

1. **Settings merging**: Stream and source settings that don't match known keys are preserved
2. **Backwards compatibility**: Handles both old (`maxref`/`minref`) and new (`maxRefValue`/`minRefValue`) field names
3. **Automatic deduction**: If not explicitly specified, `continuous` and `uniform` are inferred from data
4. **Stream map rebuilding**: `updateStreamMap()` called after deserialization to rebuild lookup structures
5. **Empty stream cleanup**: Empty-named streams are removed during deserialization

```cpp
void from_json(const JSON& j, GraphJson::SourceJson& p)
{
  // Reset to defaults
  p.m_name = "";
  p.m_streams.clear();
  p.m_settings = {};
  
  // Parse each field
  for (auto& [k, v] : j.items()) {
    if (k == "settings") {
      // Extract streams, automations, conditions from settings
      for (auto& [k2, v2] : v.items()) {
        if (k2 == "streams") {
          v2.get_to(p.m_streams);  // Deserialize stream array
        } else if (k2 == "automations") {
          v2.get_to(p.m_automations);
        } else if (k2 == "conditions") {
          v2.get_to(p.m_conditions);
        } else {
          p.m_settings[k2] = v2;  // Store other settings
        }
      }
    }
    // ... handle other fields
  }
  
  // Clean up and rebuild maps
  p.updateStreamMap();
}
```

---

## Complete Example

### Creating and Serializing a Pipeline

```cpp
#include <PhoenixCore/DataTypes/GraphJson.hpp>
#include <nlohmann/json.hpp>

using JSON = nlohmann::json;

int main() {
    // Create root graph
    GraphJson graph;
    graph.name() = "550e8400-e29b-41d4-a716-446655440000";
    graph.version() = "1.0";
    graph.date() = "2026-03-03T10:30:00Z";
    graph.mode() = "analysis";
    graph.metadata()["author"] = "System";
    
    // Create producer source (sensor device)
    GraphJson::SourceJson sensorSource;
    sensorSource.name() = "550e8400-e29b-41d4-a716-446655440001";
    sensorSource.logicalName() = "Primary Sensor";
    sensorSource.type() = "a1b2c3d4-e5f6-47a8-b9c0-d1e2f3a4b5c6";  // Component UUID
    sensorSource.isEnabled() = true;
    sensorSource.settings()["port"] = "COM3";
    sensorSource.settings()["baudrate"] = 115200;
    
    // Create stream (empty sources = producer creates it)
    // Use path-format naming: <componentName>/<streamId>
    GraphJson::StreamJson tempStream;
    tempStream.name() = "PrimarySensor/Temperature";
    tempStream.originSource() = "PrimarySensor";  // Set to component identifier
    tempStream.logicalName() = "Ambient Temperature";
    tempStream.dataType() = "number";
    tempStream.messageType() = "entry";
    tempStream.isUniform() = true;
    tempStream.isContinuous() = true;
    tempStream.delta() = 1.0f;  // 1 Hz
    tempStream.limits().min = -40.0;
    tempStream.limits().max = 125.0;
    tempStream.limits().warn = 70;
    tempStream.limits().alert = 90;
    tempStream.sources().clear();  // Empty sources = producer creates this
    sensorSource.streams().push_back(tempStream);
    
    // Add condition to sensor source
    GraphJson::ConditionJson tempHighCondition;
    tempHighCondition.id() = "cond-temp-high";
    tempHighCondition.displayName() = "High Temperature";
    tempHighCondition.type() = "limit_warn";
    tempHighCondition.isEnabled() = true;
    GraphJson::StreamJson::stream_srcitem_t arg;
    arg.srcname = sensorSource.name();
    arg.streamname = "PrimarySensor/Temperature";  // Use path convention
    arg.streamlogicalname = "Ambient Temperature";
    tempHighCondition.arguments().push_back(arg);
    tempHighCondition.settings()["sustain"] = 5.0;
    tempHighCondition.settings()["release"] = 2.0;
    sensorSource.conditions().push_back(tempHighCondition);
    
    // Add automation
    GraphJson::AutomationJson alertAutomation;
    alertAutomation.id() = "auto-alert";
    alertAutomation.displayName() = "Temperature Alert";
    alertAutomation.type() = "notify";
    alertAutomation.isEnabled() = true;
    alertAutomation.conditions().insert("cond-temp-high");
    alertAutomation.conditionConnective() = "OR";
    alertAutomation.settings()["recipients"] = JSON::array({"operator@example.com"});
    sensorSource.automations().push_back(alertAutomation);
    
    // Update internal maps
    sensorSource.updateStreamMap();
    
    // Add source to graph
    graph.sources().push_back(sensorSource);
    graph.updateSourceMap();
    
    // Serialize to JSON
    JSON jsonGraph;
    to_json(jsonGraph, graph);
    
    std::string jsonString = jsonGraph.dump(2);
    std::cout << jsonString << std::endl;
    
    // Deserialize from JSON
    JSON loadedJson = JSON::parse(jsonString);
    GraphJson loadedGraph;
    from_json(loadedJson, loadedGraph);
    
    // Verify
    std::cout << "Loaded graph: " << loadedGraph.name() << std::endl;
    std::cout << "Sources: " << loadedGraph.sources().size() << std::endl;
    
    return 0;
}
```

### Resulting JSON Output

```json
{
  "name": "550e8400-e29b-41d4-a716-446655440000",
  "version": "1.0",
  "mode": "analysis",
  "date": "2026-03-03T10:30:00Z",
  "properties": {},
  "metadata": {
    "author": "System"
  },
  "datasources": [
    {
      "name": "550e8400-e29b-41d4-a716-446655440001",
      "logicalName": "Primary Sensor",
      "type": "a1b2c3d4-e5f6-47a8-b9c0-d1e2f3a4b5c6",
      "enabled": true,
      "metadata": {},
      "settings": {
        "port": "COM3",
        "baudrate": 115200,
        "streams": [
          {
            "name": "PrimarySensor/Temperature",
            "logicalName": "Ambient Temperature",
            "originSource": "PrimarySensor",
            "type": "stream",
            "dtype": "number",
            "mtype": "entry",
            "delta": 1.0,
            "continuous": true,
            "uniform": true,
            "enabled": true,
            "sources": [],
            "limits": {
              "min": -40.0,
              "max": 125.0,
              "warn": 70,
              "alert": 90
            },
            "metadata": {}
          }
        ],
        "conditions": [
          {
            "id": "cond-temp-high",
            "display_name": "High Temperature",
            "type": "limit_warn",
            "is_enabled": true,
            "arguments": [
              {
                "sourcename": "550e8400-e29b-41d4-a716-446655440001",
                "streamname": "PrimarySensor/Temperature",
                "streamlogicalname": "Ambient Temperature"
              }
            ],
            "settings": {
              "sustain": 5.0,
              "release": 2.0
            }
          }
        ],
        "automations": [
          {
            "id": "auto-alert",
            "display_name": "Temperature Alert",
            "type": "notify",
            "is_enabled": true,
            "condition_connective": "OR",
            "conditions": ["cond-temp-high"],
            "settings": {
              "recipients": ["operator@example.com"]
            }
          }
        ]
      },
      "metadata": {}
    }
  ]
}
```

---

## AutomationsLib Reference

The `AutomationsLib` component library provides built-in condition and automation implementations for common use cases.

### LimitCondition

Located in `phoenix/Components/AutomationsLib/LimitCondition.hpp`:

**Purpose**: Evaluates whether a stream's data values fall within, exceed, or are below defined limit intervals.

#### Supported Types

Three limit condition types based on stream's `stream_limits` structure:

| Type | Interval | Description |
|------|----|-------------|
| `"limit_raw"` | `[min, max]` | Raw limit interval from stream limits |
| `"limit_warn"` | Scaled to `warn%` | Warning threshold interval |
| `"limit_alert"` | Scaled to `alert%` | Alert threshold interval |

#### Settings

```json
{
  "inverted": false,    // Boolean: Negate the condition result
  "sustain": 2.0,       // Seconds: How long condition must be true before state change
  "release": 1.0        // Seconds: How long condition must be false before state change
}
```

#### Implementation Details

From `LimitCondition.cxx`:

```cpp
LimitCondition::LimitCondition(GraphJson::ConditionJson& conditionJson, 
                               const GraphJson::StreamJson& stream, 
                               const uint32_t& longID)
  : Condition(conditionJson), m_monitoredStreamJson(stream)
{
  // Extract settings from JSON
  auto& settings = conditionJson.settings();
  m_inverted = PhoenixJson::getBool(settings["inverted"], false);
  m_sustainTimeNS = static_cast<uint64_t>(1E9 * PhoenixJson::getDouble(settings["sustain"], 0.0));
  m_releaseTimeNS = static_cast<uint64_t>(1E9 * PhoenixJson::getDouble(settings["release"], 0.0));
  
  // Build interval from stream limits
  const auto& limits = m_monitoredStreamJson.limits();
  if (conditionJson.type() == LimitConditionTypes::LIMIT_RAW) {
    m_limitInterval = Interval<double>{
      .lowerBound = limits.min, 
      .upperBound = limits.max
    };
  } else if (conditionJson.type() == LimitConditionTypes::LIMIT_WARN) {
    // Scale interval for warning threshold
    m_limitInterval = scaleIntervalForLimits(limits.min, limits.max, limits.warn / 100.0);
  } else if (conditionJson.type() == LimitConditionTypes::LIMIT_ALERT) {
    // Scale interval for alert threshold
    m_limitInterval = scaleIntervalForLimits(limits.min, limits.max, limits.alert / 100.0);
  }
}

void LimitCondition::evaluate(const JSON& runtimeArgs)
{
  // Get stream data from runtime arguments
  // Extract limited quantity (magnitude for complex, y for Point2D, etc.)
  // Apply sustain/release timing
  // Update condition state
}
```

#### Data Type Handling

LimitCondition automatically handles multiple data types:

- **`DATA_DOUBLES`, `DATA_FLOATS`**: Direct use as limit values
- **`DATA_CDOUBLES`, `DATA_CFLOATS`**: Convert to magnitude via `std::abs()`
- **Message types**: 
  - `MSG_POINT_DATA_2D`: Extract y-component
  - `MSG_POINT_DATA_3D`: Extract y and z components
  - Uniform vs non-uniform sampling handled differently

#### Example Usage

```json
{
  "id": "cond-pressure-high",
  "display_name": "High Pressure Warning",
  "type": "limit_warn",
  "is_enabled": true,
  "arguments": [
    {
      "sourcename": "550e8400-e29b-41d4-a716-446655440001",
      "streamname": "Pressure",
      "streamlogicalname": "System Pressure"
    }
  ],
  "settings": {
    "inverted": false,
    "sustain": 2.0,
    "release": 1.0
  }
}
```

### SingleCallbackAutomation

Located in `phoenix/Components/AutomationsLib/SingleCallbackAutomation.hpp`:

**Purpose**: Wraps application-specific callback functions as automation actions.

#### Interface

```cpp
class SingleCallbackAutomation : public Automation {
public:
  typedef std::function<void(std::optional<bool>, std::optional<bool>)> CallbackType;
  
  SingleCallbackAutomation(GraphJson::AutomationJson& autoJson, CallbackType callback)
    : Automation(autoJson), m_callback(callback) { }
  
  virtual void execute() override { 
    m_callback(m_currentEvaluation, m_lastEvaluation); 
  }
};
```

#### Callback Parameters

```cpp
void myCallback(std::optional<bool> currentEvaluation, 
                std::optional<bool> lastEvaluation)
{
  // currentEvaluation: Result of current condition evaluation
  // lastEvaluation: Result of last evaluation (for state change detection)
}
```

#### Example Usage

```cpp
// Create automation with callback
auto callback = [](std::optional<bool> current, std::optional<bool> last) {
    if (current && !last) {  // False -> True transition
        std::cout << "Automation triggered!" << std::endl;
        // Send notification, log event, etc.
    }
};

GraphJson::AutomationJson autoJson;
autoJson.id() = "auto-alert";
autoJson.displayName() = "Alert Automation";
autoJson.type() = "notify";
autoJson.conditions().insert("cond-temp-high");

auto automation = std::make_unique<SingleCallbackAutomation>(autoJson, callback);
```

---

## Best Practices

### 1. Naming Conventions

```cpp
// ✓ Good: Use UUIDs for component instance names
source.name() = "550e8400-e29b-41d4-a716-446655440001";

// ✓ Good: Use UUIDs for component type
source.type() = "39d09444-da10-426c-a8d1-40e605008c15";

// ✓ Good: Use path-format for stream names
stream.name() = "PrimarySensor/Temperature";
stream.originSource() = "PrimarySensor";

// ✓ Good: Use logical names for display
source.logicalName() = "Primary Sensor Array";
stream.logicalName() = "Ambient Temperature Reading";

// ✗ Avoid: Using display names as component instance identifiers
source.name() = "My Cool Sensor";  // Use UUID instead
source.type() = "device";          // Use component UUID, not class category

// ✗ Avoid: Flat stream names without path
stream.name() = "Temperature";     // Use format: <origin>/<streamId>
```

### 2. Stream Origin Management

```cpp
// ✓ Producer stream (empty sources) - component creates raw data
GraphJson::StreamJson rawStream;
rawStream.name() = "SensorDevice1/RawData";         // Path format
rawStream.originSource() = "SensorDevice1";          // Set origin
rawStream.sources().clear();                         // Empty = producer creates this
source.streams().push_back(rawStream);

// ✓ Processor stream (specifies sources) - depends on other stream
GraphJson::StreamJson filteredStream;
filteredStream.name() = "SensorDevice1/FilterProcessor1/FilteredData";  // Extended path
filteredStream.originSource() = "SensorDevice1";
stream_srcitem_t dependency;
dependency.srcname = sourceName;
dependency.streamname = "SensorDevice1/RawData";     // Path format
filteredStream.sources().push_back(dependency);
source.streams().push_back(filteredStream);
```

### 3. Update Lookup Maps

```cpp
// Always rebuild maps after modifying sources/streams
source.updateStreamMap();    // After adding/removing streams
graph.updateSourceMap();     // After adding/removing sources
```

### 4. Condition Configuration

```cpp
// Use AutomationsLib types for standard conditions
condition.type() = LimitConditionTypes::LIMIT_WARN;  // "limit_warn"

// Configure sustain/release appropriately
condition.settings()["sustain"] = 2.0;   // Debounce interval
condition.settings()["release"] = 1.0;   // Recovery interval
```

### 5. Automation Logic

```cpp
// Clearly specify condition connective
automation.conditionConnective() = "AND";  // All must be true
automation.conditionConnective() = "OR";   // Any must be true

// Always enable/disable appropriately
automation.isEnabled() = true;
condition.isEnabled() = true;
```

### 6. Round-trip Verification

```cpp
// Always test serialization round-trip
GraphJson original = buildGraph();
JSON jsonObj; to_json(jsonObj, original);
std::string jsonStr = jsonObj.dump();

JSON loadedJson = JSON::parse(jsonStr);
GraphJson loaded; from_json(loadedJson, loaded);

// Verify key fields match
assert(original.name() == loaded.name());
assert(original.sources().size() == loaded.sources().size());
```

---

## Integration with ComponentManager

The ComponentManager uses GraphJson to:

1. **Parse pipeline definition** from JSON file or object
2. **Instantiate components** based on source types and UUIDs
3. **Connect data flows** using stream dependencies
4. **Configure components** using source and stream settings
5. **Initialize conditions and automations** for monitoring and alerting

See [Component.md](Component.md) for detailed information on ComponentManager integration.
