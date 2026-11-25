# PhoenixOPCUA — DXNet Bridge (OPC UA) User Guide

`DXNet Bridge (OPC UA)` (`PhoenixOPCUA`, executable `dx+opcua.exe`) is a high-performance desktop application and bidirectional bridge between industrial **OPC UA** automation servers/clients and **Apex Phoenix DXPNet** ZeroMQ telemetry streams.

---

## 1. System Overview

```text
+---------------------------------------------------------------------------------------+
|                                DXNet Bridge (OPC UA)                                  |
|                                                                                       |
|  [OPC UA Server(s)]  <--- opc.tcp --->  - Shared OPC UA Connections Table             |
|   - DCS / SCADA / PLC                   - Read Mode (OPC UA -> DX+ Port 27126)        |
|   - Anonymous / User / X.509 Certs      - Write Mode (DX+ -> OPC UA)                  |
|                                           * Direct External Server Node Write         |
|  [External OPC UA Clients] --->           * Embedded Read-Only Mirror Server (:4850)  |
|                                           * Vector Reductions, Scale/Clamp,           |
|                                             Boolean Conditions & Max Write Rate (Hz)  |
+---------------------------------------------------------------------------------------+
```

### Core Operational Modes
1. **Read Mode (`OPC UA -> DX+`)**:
   - Connects to one or more industrial OPC UA servers.
   - Browses the address space (`Objects`), discovers variable nodes, data types, and access levels, and filters out diagnostic system nodes by default.
   - Subscribes to selected nodes using OPC UA Monitored Items.
   - Publishes samples formatted as 30-byte Phoenix binary `Message` envelopes over DXPNet (default responder port **`27126`**) with automatic mDNS service announcement (`_dxnet._tcp.local`).
2. **Write Mode (`DX+ -> OPC UA`)**:
   - Discovers running DXPNet transmitters via mDNS or connects directly by `Host:Port` (default `127.0.0.1:27126`).
   - Unrolls **Hierarchical Composite Streams** (`DATA_MESSAGES`), allowing individual sub-channels (e.g., `Max`, `Min`, `RMS`) to be mapped to distinct OPC UA variables.
   - Supports two inbound publishing targets:
     - **External Server Write (`write` mode)**: Double-click any writable variable from a connected external OPC UA server to map a DXPNet stream to it.
     - **Embedded Read-Only Mirror Server (`mirror` mode)**: Publishes DXPNet streams to `DXNet Bridge (OPC UA)`'s built-in OPC UA server (default `opc.tcp://0.0.0.0:4850/opcua/bridge/mirror/`) under `Objects/BridgeMirror`. External SCADA/DCS clients can connect and subscribe without requiring writable tags on any plant server.
   - Supports per-channel **Vector Reductions**, **Linear Scale / Clamp**, **Boolean Threshold Conditions (`Value > X && Value < Y`)**, and **Max Write Rate (Hz)** throttling.

---

## 2. Ports & Network Protocol Reference

| Service | Default Port | Port Range | Transport | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **DXNet Transmit Service (`OPC UA -> DX+`)** | `27126` | `27126` – `27176` | ZeroMQ REP + PUB | Control responder (`27126`) and high-rate publisher (`27127`). |
| **DXNet Receive Discovery (`DX+ -> OPC UA`)** | `27126` | `27126` – `27176` | ZeroMQ REQ + SUB | Target responder port to query `get_streams` / `PUB_INFO`. |
| **Embedded Mirror OPC UA Server** | `4850` | `1024` – `65535` | `opc.tcp` | Read-only mirror endpoint (`opc.tcp://0.0.0.0:4850/opcua/bridge/mirror/`). |
| **Standard OPC UA Server / Test Server** | `4840` | `1024` – `65535` | `opc.tcp` | Default target endpoint (`opc.tcp://127.0.0.1:4840/freeopcua/server/`). |
| **mDNS Service Discovery** | `5353` | Fixed | UDP Multicast | Advertises `_dxnet._tcp.local` on `224.0.0.251:5353`. |

---

## 3. Launching the Application & Command-Line Options

```powershell
# Frozen executable
dx+opcua.exe

# From Python source
py -3 apps/OPCUA/run_client.py
```

### Command-Line Arguments

| Argument / Environment Variable | Description |
| :--- | :--- |
| `--tray` / `--minimized` / `--hidden`<br>or `PHOENIX_OPCUA_START_IN_TRAY=1` | Starts the application minimized in the Windows system tray. |
| `--no-splash` | Skips the startup splash screen. |
| `--single-instance`<br>or `PHOENIX_OPCUA_SINGLE_INSTANCE=1` | Enforces single-instance lock (`phoenix_opcua.lock`). |
| `--force` / `--ignore-lock` / `--allow-multiple` | Bypasses the single-instance lock check. |
| `--test-server` *(or `--server-test`)* | Launches the standalone **OPC UA Test Server** GUI instead of the bridge. |
| `--test-client` *(or `--client-test`)* | Launches the standalone **OPC UA Test Client** GUI instead of the bridge. |

---

## 4. Workspace Layout & Shared Connections

1. **Header Bar**:
   - **OPC UA Status Badge**: Shows active vs. configured OPC UA connections (`OPC UA: 1/1 connected`).
   - **Read / Write Mode Toggle Switch**: Click **Read (OPC UA -> DX+)** or **Write (DX+ -> OPC UA)** to switch views. Green status dots indicate active Read transmission or active Write bridging.
   - **TX / RX Counters**: Real-time transmitted (`TX`) and received (`RX`) packet counters.
   - **Load Config / Save Config**: Persist or restore the complete bridge state (`.json`).
   - **Logs Button**: Displays an unread warning/error badge count and opens the non-modal **Application Logs** viewer on demand.
2. **OPC UA Connections (Shared)**:
   - Located in the top-left panel and shared across both Read and Write modes.
   - Click **+ Add Connection** to configure an OPC UA server endpoint:
     - **Host / IP** and **Port** (e.g. `127.0.0.1` and `4840`), or full endpoint URL (`opc.tcp://127.0.0.1:4840/freeopcua/server/`).
     - **Discovery**: Click **Find Servers** to query the endpoint for advertised OPC UA application URLs.
     - **Authentication**: Choose **Anonymous**, **Username / Password**, or **Certificate (`Basic256Sha256`)** (with client certificate `.der`/`.pem` and private key paths).
   - Once added, the bridge browses the server and classifies all variable nodes into **Readable** and **Writable** channels.

---

## 5. Read Mode: Streaming OPC UA Nodes to DX+ (`OPC UA -> DX+`)

1. Switch the header toggle to **Read (OPC UA -> DX+)**.
2. In the **Readable OPC UA Channels (OPC UA -> DXNet)** table:
   - The streamlined 6-column table displays **`Source`**, **`Channel Name`**, **`Node ID`**, **`Type`**, **`Units`**, and **`Live Value`**.
   - Filter channels using the **Search** box or the **OPC UA Connection Filter** dropdown, or check **Subscribed Only** to view only active subscriptions.
   - **Show System Nodes**: Unchecked by default so standard OPC UA server diagnostic nodes (`ServerStatus`, `NamespaceArray`, etc.) do not clutter your telemetry list. System nodes are never streamed over DX+.
   - Select one or more channels using **`Ctrl+Click`**, **`Shift+Click`**, **`Ctrl+Shift+Click`**, or click **Select All**.
   - Click **Subscribe Selected** to subscribe the selected channels, **Unsubscribe Selected** to remove selected channels from the subscription set, or **Clear Subscriptions** to reset all. Subscribed rows are highlighted with a warm orange theme background (`#3d2b0d`).
3. In the **DXNet Transmit Settings (OPC UA -> DX+)** panel (top right):
   - Confirm the responder **Port** (default **`27126`**).
   - Click **Start DX+ Transmit**.
   - The **Primary URI** displays `dxnet://127.0.0.1:27126`, live node values update in the table, and any `DXPNetReceiver` on the network can discover and subscribe to the streams.

---

## 6. Write Mode: Writing or Mirroring DX+ Streams to OPC UA (`DX+ -> OPC UA`)

1. Switch the header toggle to **Write (DX+ -> OPC UA)**.
2. **Discover or Add DXNet Transmitter(s)**:
   - In the **DXNet Receive Settings (DX+ -> OPC UA)** panel (top right):
     - Click **Discover** to automatically locate running DXPNet transmitters via mDNS, **OR**
     - Enter **Host** (`127.0.0.1`) and **Port** (`27126`) and click **+ Add**.
3. **Map a Writable OPC UA Node (`write` mode)**:
   - Every writable variable discovered from your connected OPC UA servers is listed in the **Writable OPC UA Channels** table.
   - **Double-click any writable channel row** (or select it and click **Map Selected...**) to open the **Channel Mapping & Transformation** dialog:
     1. Select the **DXNet Connection**, **Source Logical Name**, and **Stream Logical Name** (including individual child channels of hierarchical `DATA_MESSAGES` streams).
     2. Configure **Vector / Array Reduction** (`Pass-Through`, `Max`, `Min`, `Average`, `RMS`, `Peak-to-Peak`, `Std Dev`, `Median`, `Sum`, `First [0]`, `Last [-1]`, `Element at Index [i]`).
     3. Configure **Value Transformation & Boolean Output**:
        - **Direct Value**: Writes the reduced value directly using the target node's OPC UA VariantType.
        - **Linear Scale / Clamp**: Applies `Scale (m)`, `Offset (b)`, and optional min/max clamping.
        - **Boolean Condition (BOOL Output)**: Evaluates `Value > X && Value < Y` (or `||` / single threshold) with hysteresis deadband and optional `!` invert, writing a `Boolean` variant.
     4. Configure **Write Rate Limiting**: Set **Max Write Rate (Hz)** with trailing-edge flush and optional **Write on value change only**.
     5. Click **Save Mapping**.
4. **Configure Embedded Read-Only Mirror Variables (`mirror` mode)**:
   - Click **Mirror Server...** in the **DXNet Receive Settings** panel to configure the embedded OPC UA server (bind host `0.0.0.0` or `127.0.0.1`, port `4850`, server name, namespace URI, and Anonymous or Username/Password authentication).
   - Click **Add Mirror Mapping...** below the Writable Channels table to publish a DXPNet stream (with full Vector Reduction, Scale/Boolean Condition, and Rate Limit options) to a read-only node under `Objects/BridgeMirror` (e.g., `ns=2;s=BridgeMirror.Speed`).
5. **Start Inbound Bridge**:
   - Click **Start Inbound Bridge**.
   - If mirror mappings are active, the embedded mirror server binds port `4850` and immediately pre-initializes all mirrored variable nodes so external OPC UA clients can browse and subscribe even before the first DXPNet packet arrives.

---

## 7. Testing Locally with Built-In OPC UA Test Tools (`--test-server` & `--test-client`)

### 7.1 Launching the OPC UA Test Server
```powershell
# From source:
py -3 apps/OPCUA/run_client.py --test-server

# Or from frozen executable:
dx+opcua.exe --test-server
```
- Hosts a local `asyncua` server at `opc.tcp://127.0.0.1:4840/freeopcua/server/`.
- Provides quick channel count presets (`10`, `25`, `50`, `100`, `250`, or **Advanced Setup...**), multi-waveform signal generators (`Sine`, `Square`, `Triangle`, `Sawtooth`, `Counter`, `Random Walk`, `Noise`), manual value editing, and a real-time **Write Audit Log** showing external client writes from `DXNet Bridge (OPC UA)`.

### 7.2 Launching the OPC UA Test Client
```powershell
# From source:
py -3 apps/OPCUA/run_client.py --test-client

# Or from frozen executable:
dx+opcua.exe --test-client
```
- Connects to any OPC UA server (including the `--test-server` on port `4840` or `DXNet Bridge (OPC UA)`'s embedded **Mirror Server** on port `4850`), browses the hierarchical address space tree, subscribes to live Monitored Items with rate (`Hz`) tracking, and writes test values to writable nodes.
