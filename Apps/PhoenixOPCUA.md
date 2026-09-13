# PhoenixOPCUA

`PhoenixOPCUA` is a high-performance desktop application and bidirectional bridge between OPC UA industrial automation systems and Apex Phoenix DXPNet ZeroMQ telemetry streams.

---

## 1. System Overview

PhoenixOPCUA bridges the gap between industrial SCADA/PLC systems (OPC UA) and Phoenix's high-speed stream processing architecture (DXPNet):

```
+-----------------------------------------------------------------------------------+
|                                  PhoenixOPCUA                                     |
|                                                                                   |
|  [OPC UA Server(s)] <---> [OPC UA Client / Writer / Mirror] <---> [Mapping Engine]|
|                                                                         ^         |
|                                                                         |         |
|  [DXPNet Receiver / Transmitter] <--------------------------------------+         |
+-----------------------------------------------------------------------------------+
```

### Core Operational Modes
1. **OPC UA $\rightarrow$ DXPNet (Client Publisher Mode)**:
   - Connects to one or more OPC UA industrial servers.
   - Discovers nodes, hierarchies, and engineering units.
   - Subscribes to value changes using monitored items.
   - Publishes samples formatted as Phoenix binary `Message` envelopes over DXPNet for consumption by `DXPNetReceiver`.
2. **DXPNet $\rightarrow$ OPC UA (Inbound Bridge Mode)**:
   - Connects to a DXPNet publisher or discovers transmitters over mDNS / subnet probing.
   - Subscribes to topics and ingests incoming `Message` payloads.
   - Supports **Hierarchical child streams** (`DATA_MESSAGES`), unpacking sub-messages (e.g., `Max`, `Min`, `RMS`) and routing them to assigned target variables.
   - Applies flexible mapping rules:
     - **Write Mode**: Writes values directly into designated variable nodes on an external target OPC UA server.
     - **Mirror Mode**: Publishes data to PhoenixOPCUA's embedded, read-only `OpcUaMirrorServer`. Ideal when external DCS/SCADA servers do not expose writable tags; external OPC UA clients simply connect to PhoenixOPCUA to subscribe to live mirrored variables.

---

## 2. Ports and Network Protocol

| Service | Default Port | Port Range | Transport | Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Outbound DXPNet Service** | `27126` | `27126` – `27176` | ZeroMQ REP + PUB | Control responder and high-rate publisher. |
| **Inbound DXPNet Target** | `27126` | `27126` – `27176` | ZeroMQ REQ + SUB | Target port to connect/query for streams. |
| **Mirror OPC UA Server** | `4850` | `1024` – `65535` | `opc.tcp` | Embedded read-only mirror server (`opc.tcp://0.0.0.0:4850/opcua/bridge/mirror/`). Host, port, and security configurable via setup dialog. |
| **mDNS Service Discovery** | `5353` | Fixed | UDP Multicast | Advertises as `PhoenixOPCUA` on `_dxnet._tcp.local`. |

---

## 3. Operator Guide

### 3.1 Launching the Application
```powershell
python apps/OPCUA/run_client.py
```
Or via the test runner launcher:
```powershell
python tests/apps/run_test_apps.py --app bridge
```

### 3.2 OPC UA $\rightarrow$ DXPNet Setup
1. Open the **OPC UA $\rightarrow$ DXPNet** tab.
2. Click **Add Connection** to specify an OPC UA server endpoint (e.g., `opc.tcp://127.0.0.1:4840/freeopcua/server/`), authentication (Anonymous, Username/Password, or Certificates), and security policy.
3. Click **Browse / Refresh Nodes** to populate the stream catalog.
4. Check the boxes in the **Subscribe** column for variables you wish to stream.
5. In the **DX+ Service Control** section, confirm the control port (default `27126`) and click **Start DX+ Service**.
6. Data is now streaming over DXPNet and discoverable by any `DXPNetReceiver`.

### 3.3 DXPNet $\rightarrow$ OPC UA Setup & Child Stream Mapping
1. Open the **DXPNet $\rightarrow$ OPC UA** tab.
2. Under **DX+ Stream Discovery**, click **Discover Services** to automatically find running DXPNet transmitters on the network, or specify the responder port (default `27126`) and click **Connect DX+**.
3. **Mirror Server Setup (Optional)**: Click **Set Up Mirror Server** to configure PhoenixOPCUA's embedded read-only OPC UA server options:
   - **Host & Port**: Select binding host (`0.0.0.0`, `127.0.0.1`) and port (default `4850`).
   - **Server Name & Namespace**: Customize server identity and namespace URI.
   - **Authentication**: Choose between Anonymous access or require Username / Password authentication.
   - **Read-Only Publishing**: Mirrored variables are strictly read-only to external clients, protecting telemetry integrity.
   - *Note*: Settings persist automatically in `QSettings` across relaunches and are exported in configuration JSON files.
4. Under **Target Connections**, add at least one external OPC UA connection if using `write` mode.
5. In the **Inbound Mapping Rules** table:
   - Select a discovered stream and click **Add Mapping**.
   - **Hierarchical Streams**: If the stream contains child streams (Message data type), select the parent stream to automatically expand and map individual child streams (e.g. `Stream [Max]`, `Stream [Min]`), or map each child stream to its own distinct OPC UA Node ID.
   - **Mode Selection**:
     - `write`: Sends updates to an external target OPC UA server tag.
     - `mirror`: Publishes to PhoenixOPCUA's embedded mirror server under `Objects/BridgeMirror`. The **OPC UA Server / Target Connection** column automatically populates with the mirror endpoint URL.
   - **Tooltips**: Hover over any column header or table cell for clear descriptions of modes, stream sources, target server endpoints, and data types.
6. Click **Start Inbound Bridge**:
   - If `mirror` mode rows are present, the embedded mirror server starts, binds the configured port, and immediately pre-initializes all mirror variable nodes under `Objects/BridgeMirror` with typed initial values (`0.0`, `0`, `False`, `""`).
   - External OPC UA clients can immediately connect, browse, and subscribe to mirror variables before telemetry arrives.

### 3.4 Saving and Loading Mapping Configurations
- **Save Configuration**: Click **Save JSON** in the Inbound Mapping Rules section. The application exports a complete configuration file including subscriber endpoints, topics, target connections, mirror server setup (host, port, security credentials), and stream mappings.
- **Load Configuration**: Click **Load JSON** to load a previously saved configuration. PhoenixOPCUA restores all mapping rows, child field assignments, connection targets, and mirror server parameters seamlessly.

---

## 4. Discoverability
PhoenixOPCUA automatically registers its service on the local network via mDNS:
- **Service Type**: `_dxnet._tcp.local`
- **Instance Name**: `PhoenixOPCUA` (or `PhoenixOPCUA_<port>`)
- **TXT Record**: Contains protocol (`tcp`), UUID, port, and password requirements.
This enables automatic detection by `DXPNetReceiver` and the `dxpnet_receiver_app` test utility.
