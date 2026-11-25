# Phoenix Network Port Allocation & Port Registry

This document establishes the official port assignment and port ranges for Phoenix network protocols, core services, licensing, and remote management.

---

## 1. Port Allocation Table

| Service / Protocol | Default Port | Reserved / Assigned Range | Transport / Sockets | Description |
| :--- | :--- | :--- | :--- | :--- |
| **DSNet Protocol** | `10995` | `10990` – `10999` | TCP / DSAPI | APEX DS server network stream transport protocol. |
| **Remote Component Manager** | `27100` | `27100` – `27125` | ZeroMQ REP / REQ | Remote component management, control commands, graph lifecycle. |
| **DXPNet Protocol** | `27126` | `27126` – `27176` | ZeroMQ REQ/REP + PUB/SUB | High-throughput stream streaming protocol for Phoenix. |
| **PhoenixOPCUA Outbound** | `27126` | `27126` – `27176` | ZeroMQ REP (Control) + PUB | Default control port for PhoenixOPCUA outbound DXPNet publisher service (`PUB` on `27127`). |
| **PhoenixOPCUA Inbound Target** | `27126` | `27126` – `27176` | ZeroMQ REQ (Control) + SUB | Default target discovery port for inbound DXPNet subscriber. |
| **PhoenixOPCUA Mirror Server** | `4850` | `1024` – `65535` | `opc.tcp` | Embedded read-only OPC UA mirror server (`opc.tcp://0.0.0.0:4850/opcua/bridge/mirror/`). |
| **PhoenixPLC Outbound** | `27136` | `27126` – `27176` | ZeroMQ REP (Control) + PUB | Default control port for PhoenixPLC (`DXNet Bridge (PLC)`) outbound DXPNet publisher service (`PUB` on `27137`). |
| **EtherNet/IP (CIP)** | `44818` | `44818` | TCP & UDP (`ListIdentity`) | Standard Allen-Bradley ControlLogix EtherNet/IP CIP port (and `--test-server` default). |
| **mDNS Service Discovery** | `5353` | `5353` | UDP Multicast (`224.0.0.251`) | Multicast DNS discovery for `_dxnet._tcp.local` DXPNet services. |
| **PhoenixLM Standard Service** | `27884` | `27884` | TCP / ZeroMQ | Standard local license client service (`LOCAL` and `PASSTHROUGH` modes). |
| **PhoenixLM License Server** | `27885` | `27885` | TCP / ZeroMQ | Floating license server control service (`SERVER` mode). |
| **PhoenixLM Server Listen Port** | `27886` | `27886` | TCP / ZeroMQ | Floating license server listener for remote `PASSTHROUGH` daemons. |
| **ApexLM Local Web Admin UI** | `27887` | `27887` | Loopback HTTP | Local daemon Web Admin UI & `/api/v1` management API (`127.0.0.1`). |
| **ApexLM Server Web Admin UI** | `27888` | `27888` | Loopback HTTP | Floating server daemon Web Admin UI & `/api/v1` API (`127.0.0.1`). |

---

## 2. Detailed Service Specifications

### 2.1 DXPNet Protocol (`27126` – `27176`)
- **Default Port**: `27126`
- **Assigned Port Range**: `27126` to `27176` (51 ports reserved for multi-instance transmitters and receivers on a single host).
- **Socket Architecture**:
  - **Control Channel (REQ / REP)**: Binds to the designated port in the range (e.g. `tcp://0.0.0.0:27126`). Handles `PUB_INFO`, `get_streams`, `query_graphtime`, and `graph` requests.
  - **Streaming Data Channel (PUB / SUB)**: Binds to an ephemeral port (reported in `PUB_INFO`) or multicast group (`epgm://`) with High Water Mark default of `1,000,000` (1M) and OS send/receive buffers up to `16 MB`.
- **Discovery**: Services advertise on mDNS (`_dxnet._tcp.local`) or can be discovered by querying `PUB_INFO` across the reserved port range `27126`–`27176`.

### 2.2 Remote Component Manager (`27100` – `27125`)
- **Default Port**: `27100`
- **Assigned Port Range**: `27100` to `27125` (26 ports reserved).
- **Socket Architecture**: ZeroMQ `REP` socket listening for component lifecycle orchestration, status requests, and remote automation commands via `ComponentRemoteManager`.

### 2.3 DSNet Protocol (`10995`)
- **Default Port**: `10995`
- **Assigned Port Range**: `10990` to `10999`.
- **Socket Architecture**: Proprietary APEX DSAPI network protocol used by `DSNetReceiver` for connecting to legacy and high-speed DS acquisition hardware.

### 2.4 Licensing (PhoenixLM / ApexLM: `27884` – `27888`)
- **Default Standard Port (`27884`)**: Used by `ApexLmClientLocal` and the local background service (**APEX LM Daemon**, `ApexLM_Service`) for license validation in `LOCAL` and `PASSTHROUGH` modes.
- **Default Server Port (`27885`) / External Listen (`27886`)**: Used by the enterprise floating license manager service (**APEX LM Server Daemon**, `ApexLM_Server_Service`, `SERVER` mode) to service remote `PASSTHROUGH` daemon checkout, reservation, and heartbeat requests across the network.
- **Web Admin UI Ports (`27887` / `27888`)**: Loopback-only (`127.0.0.1`) HTTP management UI & REST APIs for workstation (`27887`) and floating server (`27888`) administration. See **[APEX License Manager — Workstation & Server Setup](../../../License%20Manager/setup-and-server.md)** and **[Web UI & Reservations](../../../License%20Manager/web-ui-and-reservations.md)** *(legacy FLEXlm / `lmgrd` `27000` is discontinued October 2027)*.

---

## 3. Implementation Guidelines for Applications
1. Applications implementing DXPNet transmitters must default to port `27126`.
2. When starting multiple transmitters on the same host, sequentially allocate from the range `27126`–`27176`.
3. Automated port scanners and discovery routines should scan within `27126`–`27176` when mDNS multicast is unavailable or restricted by firewall rules.
