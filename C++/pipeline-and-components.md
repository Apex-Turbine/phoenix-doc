# C++ Pipelines, Components & `StreamTableManager`

Execute real-time streaming pipelines or offline file/database replay graphs using **`GraphJson`**, **`TableJson`**, **`StreamTableManager`**, and **`ComponentManager`**.

## Pipeline Configuration: `TableJson` vs. `GraphJson`

Phoenix supports two JSON configuration models:
- **`GraphJson` ([reference](../PhoenixCore/GraphJson.md))**: Native node-and-edge DAG (`datasources` of `SourceJson`, each containing `StreamJson`, `ConditionJson`, `AutomationJson`, and upstream `stream_srcitem_t` links).
- **`TableJson` ([reference](../PhoenixCore/TableJson.md))**: Channel/stream-centric tabular setup used by setup UIs (`inputs`, `processing_stages`, `streams`, `outputs`). Convert any `TableJson` to an executable `GraphJson` with `StreamTableManager::buildGraphFromTable(tableJson)`.

```cpp
#include <PhoenixCore/DataTypes/TableJson.hpp>
#include <PhoenixCore/DataTypes/GraphJson.hpp>
#include <PhoenixCore/Component/StreamTableManager.hpp>

StreamTableManager tableMgr;
GraphJson graph = tableMgr.buildGraphFromTable(tableJson);
```

## Choosing a `ComponentManager` Implementation

| Class | Use Case | Behavior |
| --- | --- | --- |
| `ComponentManager` | Base graph execution engine | Instantiates sources via `ComponentLibraryManager`, validates streams/units/types, wires `ComponentSubscriptionHandler` queues, manages lifecycle |
| `OnlineComponentManager` | Real-time acquisition & monitoring | Runs asynchronous multi-threaded acquisition pipelines from live hardware devices or ZeroMQ streams |
| `OfflineQueryComponentManager` | Historical / batch replay | Executes graphs over bounded time windows from files or databases (SQLite/PostgreSQL) |
| `ComponentRemoteManager` | Distributed / ZMQ remote orchestration | Controls and monitors remote pipeline instances over ZeroMQ |

## Standard Pipeline Execution Pattern

```cpp
#include <PhoenixCore/Component/ComponentLibrary.hpp>
#include <PhoenixCore/Component/OnlineComponentManager.hpp>
#include <PhoenixCore/DataTypes/GraphJson.hpp>
#include <PhoenixCore/Utilities/EventManager.hpp>

// 1. Load component plugins from disk ("GetComponents")
auto compLibMgr = getComponentLibraryManager();
compLibMgr->addPath("bin/components");

// 2. Prepare EventManager and OnlineComponentManager
auto eventMgr = std::make_shared<EventManager>();
OnlineComponentManager mgr;
mgr.setEventManager(eventMgr);

// 3. Load GraphJson configuration, validate, start, and stop cleanly
//    mgr.loadGraph(graph);
//    mgr.start();
//    ... subscribe callbacks / run until done ...
//    mgr.stop();
```

## `Component` Lifecycle & State Machine (`Component.hpp`)

Built-in and custom components expose a standardized lifecycle and health state string (`m_health["status"]`):

- **Health State Macros**: `COMP_NEW` (`"unconfigured"`), `COMP_IMPORT` (`"importing"`), `COMP_CONFIGURE` (`"configuring"`), `COMP_READY` (`"ready"`), `COMP_STARTING` (`"starting"`), `COMP_RUNNING` (`"running"`), `COMP_STOPPING` (`"stopping"`), `COMP_STOPPED` (`"stopped"`), `COMP_ERROR` (`"error"`), `COMP_UPDATING` (`"updating"`), `COMP_RESETTING` (`"resetting"`), `COMP_RECORDING` (`"recording"`), `COMP_EVENT_RECORDING` (`"event_recording"`), `COMP_COMPLETED` (`"completed"`).
- **Virtual Lifecycle & Processing Methods**:
  - `setup(graph, srcInfo)` — Initialize component settings, streams (`setupStreams`, `validateStreams`), and automations (`setupAutomations`, `buildCondition`, `buildAutomation`)
  - `import(sj, parents)` — Discover/import streams from connected hardware or files into `SourceJson`
  - `start()`, `stop()`, `dataReset()`, `reset()` — Execution lifecycle hooks
  - `process(const Message& msg)` — Pure virtual handler invoked on incoming upstream messages
  - `info()` — Pure virtual returning `const componentInfo_t&` metadata, supported domains/units, data types, and message types
  - `commands()`, `requests()`, `command(PhoenixCommand)`, `request(PhoenixRequest)` — Interactive RPC operations (zeroing, recording toggle, status queries)
  - `sendMsg(msg)` / `sendMsgs(msgs)` — Emit output `Message` envelopes to downstream subscribers

See [Component Architecture](../PhoenixCore/Component.md) for component wiring and `ComponentSubscriptionHandler` queue mechanics.
