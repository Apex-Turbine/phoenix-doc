# PhoenixEngine — Pipeline Server User Guide

> [!NOTE]
> `PhoenixEngine` (`apps/PhoenixEngine/phoenix.py`) is the legacy FastAPI/REST and system-tray pipeline server for executing C++ `PhoenixCore` signal-processing DAG graphs and querying data stores over HTTP. Licensing uses **[APEX License Manager](../../../License%20Manager/README.md)** (legacy FLEXlm discontinued **October 2027**), and API/format usage is governed by the **[Software License Agreement](../../../Release%20Policies/software-license-agreement.md)**.

---

## 1. Overview

`PhoenixEngine` hosts a local or network-accessible HTTP REST API (powered by **FastAPI** and **Uvicorn**) alongside an optional desktop system-tray window:

1. **Signal Processing Graph Execution (`/api/graph` & `/api/v2`)**:
   - Load, configure, start, pause, step, and stop `PhoenixCore` component graphs (`GraphJson`).
   - Connect hardware acquisition inputs, DSP processors (`FFTProcessor`, `FIRFilter`, `IIRFilter`, `OrderFFT`, `Statistics`, `Triggers`), and output writers/transmitters (`DX+Transmit`, `SQLiteOutput`, `PostgreSQLOutput`, `RWXFileWriter`, `DATXFileWriter`).
2. **Telemetry Store Queries (`/api/store` & `/api/v2`)**:
   - Connect to Phoenix databases and recording files to query record catalogs, stream descriptors, time-series data slices, and discrete events.
3. **Component & Adapter Discovery (`/api/info`)**:
   - Inspect all loaded C++ component plugins, database adapters, file adapters, and stream adapters along with their identifiers and attribute schemas.

---

## 2. Launching PhoenixEngine & CLI Options

Run `phoenix.py` from the repository root:

```powershell
py -3 apps/PhoenixEngine/phoenix.py [OPTIONS]
```

### Command-Line Options

| Option | Default | Description |
| :--- | :--- | :--- |
| `--port <int>` | `8000` | HTTP TCP port for the FastAPI/Uvicorn server. |
| `--host <str>` | `127.0.0.1` | Bind IP address (`127.0.0.1` when `--local` is enabled, or `0.0.0.0` for network access). |
| `--local / --no-local` | `--local` | Restrict server binding to localhost (`127.0.0.1`). |
| `--api-version <int>` | `1` | Select REST API route version (`1` for `/api/*` or `2` for `/api/v2/*`). |
| `--debug / --no-debug` | `--no-debug` | Enable verbose debug logging. |

---

## 3. Using the REST API & Interactive Swagger UI

Once `PhoenixEngine` is running (default `http://127.0.0.1:8000`):

1. **Interactive OpenAPI Documentation**:
   - Open **`http://127.0.0.1:8000/docs`** in your web browser to explore and test all endpoints interactively via Swagger UI.
   - Open **`http://127.0.0.1:8000/redoc`** for ReDoc API reference documentation.
2. **Primary Endpoint Groups**:
   - **`GET /api/info/components`**: List all available C++ processing components and port schemas.
   - **`GET /api/info/databases` / `GET /api/info/files` / `GET /api/info/streams`**: List registered storage and streaming adapters.
   - **`POST /api/graph`**: Create a new DAG graph session from a `GraphJson` configuration payload.
   - **`POST /api/graph/{session_id}/start` / `stop` / `pause`**: Control real-time pipeline execution.
   - **`POST /api/store`**: Open a database or file reader session and query records, streams, and events.
3. **System Tray Supervisor**:
   - When running in desktop mode, the Phoenix icon appears in the Windows system tray, allowing you to view server status, inspect the bound port, or cleanly shut down the Uvicorn server.
