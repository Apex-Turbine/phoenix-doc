# Phoenix Applications — User & Operator Guides

Welcome to the user and operator documentation for the **APEX Phoenix Applications Suite**. Each desktop application and protocol bridge in the Phoenix ecosystem is documented below with step-by-step operational workflows, configuration guides, network port references, command-line options, and troubleshooting tips.

---

## Application Guides

| Application | Binary / Entrypoint | Summary | User Manual |
| :--- | :--- | :--- | :--- |
| **DX+ DB Manager (`PhoenixFoundry`)** | `dx+dbmanager.exe`<br>`apps/PhoenixFoundry/src/main.py` | Enterprise telemetry database & file manager for inspecting `.dxdb` (SQLite), PostgreSQL, `.rwx`, and `.datx` recordings, visualizing decimated waveforms, managing events, exporting columnar CSV / binary files, and performing database maintenance & merges. | **[PhoenixFoundry.md](PhoenixFoundry.md)** |
| **DXNet Bridge (OPC UA) (`PhoenixOPCUA`)** | `dx+opcua.exe`<br>`apps/OPCUA/run_client.py` | Bidirectional industrial OPC UA $\leftrightarrow$ DXPNet bridge supporting shared OPC UA server connections, monitored item publishing (`OPC UA -> DX+`), inbound vector reductions, linear scaling, boolean threshold conditions, write rate limiting, and an embedded read-only OPC UA Mirror Server (`DX+ -> OPC UA`). | **[PhoenixOPCUA.md](PhoenixOPCUA.md)** |
| **DXNet Bridge (PLC) (`PhoenixPLC`)** | `dx+plc.exe`<br>`apps/PLC/run_client.py` | Bidirectional Allen-Bradley ControlLogix 5575 (`1756-L75`) & 5563 (`1756-L63`) EtherNet/IP CIP $\leftrightarrow$ DXPNet bridge with CIP `ListIdentity` PLC discovery, 1756 backplane slot scanning, multi-rate Poll & On-Change (COV) deadband streaming (`PLC -> DX+`), and inbound vector reductions, boolean threshold conditions, and rate-limited tag writing (`DX+ -> PLC`). | **[PhoenixPLC.md](PhoenixPLC.md)** |
| **PhoenixEngine (Legacy Pipeline Server)** | `Phoenix.exe`<br>`apps/PhoenixEngine/phoenix.py` | Legacy FastAPI REST control server and system-tray supervisor for managing C++ `PhoenixCore` signal-processing DAG graphs and data store queries over HTTP. | **[PhoenixEngine.md](PhoenixEngine.md)** |

---

## Default Network Port Summary

| Application / Service | Default Port | Transport / Protocol | Direction / Role |
| :--- | :--- | :--- | :--- |
| **DXNet Bridge (OPC UA) — DX+ Transmit** | `27126` | ZeroMQ REP (`27126`) + PUB (`27127`) | Publishes subscribed OPC UA nodes to DX+ (`dxnet://0.0.0.0:27126`). |
| **DXNet Bridge (OPC UA) — Mirror Server** | `4850` | `opc.tcp` | Embedded read-only OPC UA mirror server (`opc.tcp://0.0.0.0:4850/opcua/bridge/mirror/`). |
| **DXNet Bridge (PLC) — DX+ Transmit** | `27136` | ZeroMQ REP (`27136`) + PUB (`27137`) | Publishes polled/COV ControlLogix PLC tags to DX+ (`dxnet://0.0.0.0:27136`). |
| **DXNet Receive Discovery Target** | `27126` | ZeroMQ REQ / SUB (`27126`–`27176`) | Default target port when discovering or adding inbound DX+ transmitters. |
| **Allen-Bradley EtherNet/IP (CIP)** | `44818` | TCP & UDP (`ListIdentity` `0x0063`) | Standard Rockwell / Allen-Bradley ControlLogix EtherNet/IP port. |
| **OPC UA Standard Server** | `4840` | `opc.tcp` | Standard OPC UA server endpoint port (and `--test-server` default). |
| **mDNS Service Discovery** | `5353` | UDP Multicast (`224.0.0.251`) | Automatic discovery of `_dxnet._tcp.local` transmitters (`DX+OPCUA`, `DX+PLC`). |
| **PhoenixLM License Service** | `27884` | TCP / ZeroMQ | Local license daemon validation for `FOUNDRY-APP`, `opcua-bridge-app`, and `plc-bridge-app`. |
