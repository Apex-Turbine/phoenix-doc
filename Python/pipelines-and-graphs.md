# Building & Running Pipelines in Python

Construct DAG pipelines directly with **`GraphJson`** or tabular channel definitions with **`TableJson` + `StreamTableManager`**, then run them asynchronously or offline in `phoenixpy` (`swig/packaging/python/sample_phoenixcore.py`).

## Pipeline Modules

| Module | Key Classes | Purpose |
| --- | --- | --- |
| `ComponentLibrary` | `getComponentLibraryManager()` | Load `"GetComponents"` plugin DLLs/`so`s via `addPath(...)` |
| `GraphJson` | `GraphJson`, `SourceJson`, `StreamJson` | Build, load, inspect, and save DAG pipeline configs |
| `TableJson` | `TableJson` | Channel-centric tabular setup definitions |
| `StreamTableManager` | `StreamTableManager` | Compile `TableJson` to `GraphJson` (`buildGraphFromTable`) |
| `OnlineComponentManager` | `OnlineComponentManager` | Real-time streaming pipeline execution manager |
| `OfflineQueryComponentManager` | `OfflineQueryComponentManager` | Bounded historical file/database replay manager |
| `ComponentCallback` | `ComponentCallbackItem` | Subclass in Python (SWIG director) to receive output `Message`s |
| `EventManager` | `EventManager`, `EventHandler` | Subclass `EventHandler` in Python to capture structured logs & errors |

## Subclassing Callbacks in Python (SWIG Directors)

`ExceptionHandle.i` enables SWIG directors so Python subclasses of `EventHandler` and `ComponentCallbackItem` can receive asynchronous callbacks from Phoenix's C++ worker threads safely:

```python
from phoenixpy import EventManager, ComponentCallback, Message

class PyEventLogger(EventManager.EventHandler):
    def __init__(self):
        super().__init__()
        self.setMinLevel(EventManager.EventManager.LVL_INFO)

    def handleEvent(self, event_json):
        print(f"[EVENT] {event_json}")

class PyDataSink(ComponentCallback.ComponentCallbackItem):
    def execute(self, msg: Message.Message):
        # Inspect timestamp (ns UTC), source/stream index, data/message types
        print(f"Message src={msg.srcIdx()} stream={msg.streamIdx()} t={msg.tstamp()} size={msg.size()}")
```

## Executing a Pipeline (`sample_phoenixcore.py` pattern)

```python
import time
from phoenixpy import (
    ComponentLibrary,
    EventManager,
    GraphJson,
    OnlineComponentManager,
)

# 1. Initialize diagnostics
ev_mgr = EventManager.EventManager()
logger = PyEventLogger()
ev_mgr.addHandler(logger)

# 2. Load built-in component plugins ("GetComponents")
lib_mgr = ComponentLibrary.getComponentLibraryManager()
# lib_mgr.addPath("/path/to/bin/components")

# 3. Prepare GraphJson (either constructed programmatically or parsed from JSON)
graph = GraphJson.GraphJson()
# ... populate datasources & streams or convert a TableJson via StreamTableManager ...

# 4. Run pipeline via OnlineComponentManager
mgr = OnlineComponentManager.OnlineComponentManager()
mgr.setEventManager(ev_mgr)
# mgr.loadGraph(graph)
# mgr.start()
# time.sleep(2.0)
# mgr.stop()
```

### Converting `TableJson` to `GraphJson`

If your setup comes from a channel-centric tabular JSON configuration (such as a DX+DAQ table setup):

```python
from phoenixpy import TableJson, StreamTableManager

tbl = TableJson.TableJson()
# Load or populate tbl inputs, processing_stages, streams, outputs
stm = StreamTableManager.StreamTableManager()
graph = stm.buildGraphFromTable(tbl)
```

For batch replay over bounded time windows from files or databases, swap `OnlineComponentManager` for `OfflineQueryComponentManager`. See [GraphJson Reference](../PhoenixCore/GraphJson.md) and [TableJson Reference](../PhoenixCore/TableJson.md).
