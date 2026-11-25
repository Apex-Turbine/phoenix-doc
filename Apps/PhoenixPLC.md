# PhoenixPLC — DXNet Bridge (PLC) User Guide

`DXNet Bridge (PLC)` (`PhoenixPLC`, executable `dx+plc.exe`) is a bidirectional desktop bridge application that connects **Allen-Bradley ControlLogix 5575 (`1756-L75`)** and **ControlLogix 5563 (`1756-L63`)** Programmable Logic Controllers over **EtherNet/IP (CIP)** with **Apex Phoenix DXPNet** high-speed telemetry streams.

---

## 1. Overview & Key Capabilities

```text
+---------------------------------------------------------------------------------------+
|                                  DXNet Bridge (PLC)                                   |
|                                                                                       |
|  [Allen-Bradley ControlLogix PLCs]  <--- EtherNet/IP (CIP Port 44818) --->            |
|   - ControlLogix 5575 (1756-L75)         - Shared PLC Connections Table               |
|   - ControlLogix 5563 (1756-L63)         - Read Mode (PLC -> DX+ Port 27136)          |
|   - Controller & Program:* Tags            Periodic Poll & On-Change (COV) Deadband   |
|   - Scalars, 1D Arrays, STRING, UDTs     - Write Mode (DX+ -> PLC)                    |
|                                            Vector Reductions, Scale/Clamp,            |
|                                            Boolean Conditions & Max Write Rate (Hz)   |
+---------------------------------------------------------------------------------------+
```

### Operational Directions
1. **Read Mode (`PLC -> DX+`)**:
   - Reads Controller-scoped and `Program:<name>`-scoped tags from one or more Allen-Bradley ControlLogix PLCs.
   - Supports per-tag and bulk update modes:
     - **Poll**: Periodic sampling at a configurable interval (`20 ms` to `60,000 ms`, default `100 ms`).
     - **On-Change (COV)**: Publishes a DXPNet packet only when a tag's value changes by more than its configured **COV Deadband**.
   - Broadcasts samples as native 30-byte Phoenix binary `Message` envelopes over DXPNet (default responder port **`27136`**) with automatic mDNS announcement (`_dxnet._tcp.local`).
2. **Write Mode (`DX+ -> PLC`)**:
   - Discovers active DXPNet transmitters via mDNS or connects directly by `Host:Port` (default `127.0.0.1:27126`).
   - Allows double-clicking any writable ControlLogix tag to map an incoming DXPNet stream with:
     - **Vector / Array Reductions**: `Pass-Through`, `Max`, `Min`, `Average`, `RMS`, `Peak-to-Peak`, `Std Dev`, `Median`, `Sum`, `First [0]`, `Last [-1]`, or `Element at Index [i]`.
     - **Value Transformation & Boolean Conditions**: Direct pass-through, linear scaling (`y = m*x + b` with min/max clamping), or boolean threshold expressions (`Value > X && Value < Y` / `||` with hysteresis deadband and `!` inversion) that output `BOOL` values directly to PLC coils/flags.
     - **Write Rate Limiting**: Configurable **Max Write Rate (Hz)** with trailing-edge flush and write-on-change-only filtering to protect the PLC backplane from high-rate telemetry bursts.

---

## 2. Launching the Application & Command-Line Options

### 2.1 Standard Launch
From an installed release (`dx+/bin/`) or from source:
```powershell
# Frozen executable
dx+plc.exe

# From Python source
py -3 apps/PLC/run_client.py
```

On launch, `DXNet Bridge (PLC)` validates its license (`plc-bridge-app` via `PhoenixLic`), displays the splash screen, and opens the main workspace.

### 2.2 Command-Line Arguments

| Argument / Environment Variable | Description |
| :--- | :--- |
| `--config <path.json>` or `--config=<path.json>` | Automatically loads a saved bridge JSON configuration file on startup and connects to all configured PLCs. |
| `--tray` / `--minimized` / `--hidden`<br>or `PHOENIX_PLC_START_IN_TRAY=1` | Starts the application hidden in the Windows system tray. |
| `--no-splash` | Skips the startup splash screen. |
| `--single-instance`<br>or `PHOENIX_PLC_SINGLE_INSTANCE=1` | Enforces single-instance lock (`phoenix_plc.lock`), prompting before launching a second instance. |
| `--force` / `--ignore-lock` / `--allow-multiple` | Bypasses the single-instance lock check. |
| `--test-server` *(or `--server-test`, `--test-plc`)* | Launches the standalone **ControlLogix CIP / EtherNet/IP Test Server** GUI instead of the bridge. |
| `--test-client` *(or `--client-test`)* | Launches the standalone **ControlLogix CIP / EtherNet/IP Test Client** GUI instead of the bridge. |

---

## 3. Workspace Layout & Header Bar Controls

The main window is organized to maximize screen space for tag tables:

1. **Header Bar (Top)**:
   - **PLC Status Badge**: Displays active vs. total configured PLC connections (e.g., `PLC: 1/1 connected`).
   - **Read / Write Mode Toggle Switch**: Click **Read (PLC -> DX+)** or **Write (DX+ -> PLC)** (or press `Left`/`R` and `Right`/`W` when focused) to switch between Read and Write views. Green status dots on each side of the toggle indicate whether the DX+ Transmitter (`Read`) or Inbound Bridge (`Write`) is actively running.
   - **Live Counters**: `TX: <published> / <polled>` and `RX: <inbound_messages>`.
   - **Load Config / Save Config**: Load or save the complete bridge configuration (`.json`), including all PLC connections, Read subscriptions, DXNet endpoints, and Write mappings.
   - **Logs Button**: Displays an unread badge counter (e.g. `Logs (1)`) highlighted in orange for warnings or crimson for errors. Clicking **Logs** opens the non-modal **Application Logs** window with level filtering (`All`, `Warnings & Errors`, `Errors Only`), text search, and `Copy All`.
2. **Top Row (Shared Connections & DXNet Settings)**:
   - **Left Panel — `Allen-Bradley ControlLogix Connections (Shared)`**: Lists all configured PLCs (`Controller / Name`, `IP / Slot`, `CPU / Rev`, `Read / Write` tag counts, and `Status`). Connections added here are immediately available in both Read and Write modes.
   - **Right Panel — Stacked DXNet Settings**: Automatically switches between **DXNet Transmit Settings (PLC -> DX+)** in Read mode and **DXNet Receive Settings (DX+ -> PLC)** in Write mode.
3. **Bottom Area — Stacked Tag Tables**:
   - Switches between **Readable ControlLogix Tags (PLC -> DXNet)** in Read mode and **Writable ControlLogix Tags** in Write mode.
4. **System Tray Integration**:
   - Minimizing or closing the window minimizes `DXNet Bridge (PLC)` to the Windows system tray so background bridging continues uninterrupted. Right-click the tray icon and choose **Exit** to shut down the bridge completely.

---

## 4. Adding & Discovering Allen-Bradley ControlLogix PLCs

1. In the **Allen-Bradley ControlLogix Connections (Shared)** panel, click **+ Add PLC**.
2. Configure the connection parameters in the **Add ControlLogix PLC Connection** dialog:
   - **Controller Alias**: Optional friendly name (e.g., `TestStand_L75`). If left blank, defaults to `<IP> (Slot <N>)` or the discovered product name.
   - **Primary IP Address**: IPv4 address of the ControlLogix Ethernet module (e.g., `192.168.1.10`) or `host:port` (e.g., `127.0.0.1:44818` when connecting to the local CIP Test Server).
   - **Discover...**:
     - Click **Discover...** to scan for Allen-Bradley EtherNet/IP devices via CIP `ListIdentity` (`0x0063`) broadcast across local network adapters as well as localhost (`127.0.0.1:44818`) and any host entered in the IP box.
     - Select a discovered controller in the **Discovered EtherNet/IP Devices** table (`IP Address`, `Product Name`, `Vendor`, `Revision / Serial`) and click **Use Selected Device** (or double-click the row).
   - **CPU Chassis Slot**: The 1756 backplane slot number (`0`..`16`) where the ControlLogix processor resides (typically `0`).
   - **Scan Backplane Slots...**:
     - Click **Scan Backplane Slots...** to query slots `0` through `16` of the remote 1756 chassis over CIP and pick the CPU slot from the discovered modules list (e.g., `Slot 0: 1756-L75/A LOGIX5575`, `Slot 1: 1756-EN2T/D`).
   - **CIP Route Path**: Optional advanced CIP route override. Leave blank for standard `IP/Slot` routing.
   - **Socket Timeout**: CIP socket timeout in seconds (`1.0 s` to `30.0 s`, default `5.0 s`).
   - **Load Program-Scoped Tags (`Program:*`)**: Checked by default. Loads both Controller-scoped tags and all `Program:<ProgramName>.*` tags.
3. Click **Connect & Load Tags**.

### Automatic ControlLogix 5575 & 5563 Packet Negotiation
- The bridge first negotiates **4000-byte Extended Forward Open** packets for maximum throughput on ControlLogix 5575 (`1756-L75`) and `1756-EN2T` modules.
- If connecting to a legacy ControlLogix 5563 (`1756-L63`) or `1756-ENBT` adapter that only supports 500-byte packets, the bridge automatically falls back to **500-byte Standard Forward Open** and adjusts batch read/write chunking transparently.
- **Tag Handling**:
  - `STRING` structures are preserved as single `STRING` tags (internal `.LEN` and `.DATA` fields are hidden).
  - User-Defined Data Types (UDTs, e.g. `TurbineStatus`) are automatically expanded into individual leaf tags (`TurbineStatus.SpeedRPM`, `TurbineStatus.InWindow`, `TurbineStatus.ActiveSetpoint`).
  - Tags with CIP `ExternalAccess = None` or internal `"__*"` prefixes are excluded.
  - Tags with `ExternalAccess = Read Only` appear only in the **Read** table; tags with `ExternalAccess = Read/Write` appear in both the **Read** and **Write** tables.

---

## 5. Read Mode: Streaming PLC Tags to DX+ (`PLC -> DX+`)

1. Ensure the header toggle is set to **Read (PLC -> DX+)**.
2. **Filter & Locate Tags**:
   - Use the **Search** box to filter by tag name, scope, CIP data type, or Long ID.
   - Use the **PLC Filter** (`All PLCs` or a specific controller) and **Scope Filter** (`All Scopes`, `Controller`, `Program:TurbineControl`, etc.) dropdowns.
   - Check **Subscribed Only** to view only active tags.
3. **Configure Units, Unit Domain, Poll Mode, Poll Rate & COV Deadband**:
   - **Automatic & User-Configurable Units and Unit Domain**:
     - When tags are loaded from a ControlLogix PLC, `DXNet Bridge (PLC)` automatically infers the variable's engineering unit and physical domain when available from tag metadata or tag naming conventions (for example, `RotorSpeed_RPM` -> `RPM` / `frequency`, `ExhaustTemp_DegC` -> `DegC` / `temperature`, `TargetPressure_kPa` -> `kPa` / `pressure`, `STRING` tags -> `txt`).
     - By default, all time-series streams use **`time - seconds (s)`** as their primary domain (`units[0]`).
     - In the **`Units`** and **`Unit Domain`** columns of any row, you can select from standard Phoenix engineering units (`count`, `s`, `ms`, `us`, `Hz`, `kHz`, `RPM`, `DegC`, `DegF`, `K`, `kPa`, `psi`, `bar`, `Pa`, `MPa`, `V`, `mV`, `kV`, `A`, `mA`, `W`, `kW`, `hp`, `g`, `m/s^2`, `m/s`, `mm/s`, `ips`, `m`, `mm`, `cm`, `in`, `ft`, `mil`, `N`, `kN`, `lbf`, `kgrams`, `lbm`, `deg`, `rad`, `%`, `strain`, `ustrain`, `txt`), type a custom unit string, or change the **`Unit Domain`** (`time - seconds (s)`, `frequency`, `temperature`, `pressure`, `voltage`, `current`, `length`, `velocity`, `acceleration`, `force`, `power`, `mass`, `angle`, `strain`, `none`).
   - **Per-Row Poll & COV Configuration (Merged `Rate / Deadband` Column)**:
     - The streamlined 8-column Read Tags table displays **`PLC`**, **`Tag Name`**, **`Type`**, **`Units`**, **`Unit Domain`**, **`Mode`**, **`Rate / Deadband`**, and **`Live Value`**.
     - When **`Mode`** is set to **`Poll`**, the **`Rate / Deadband`** column displays the poll interval in milliseconds (`20`–`60000 ms`).
     - When **`Mode`** is set to **`On-Change (COV)`**, the **`Rate / Deadband`** column automatically switches to the Change-of-Value deadband threshold (`± deadband`).
   - **Bulk Configuration**: Select multiple rows (`Ctrl+Click`, `Shift+Click`, or click **Select All**), choose the desired `Units`, `Domain`, `Mode`, and `Rate (ms)` / `Deadband` in the top toolbar, and click **Apply to Selected**.
   - **DXNet Stream Naming & Grouping**:
     - Streams published over DXNet use the clean PLC variable name (`<Variable Name>`, without `::`) as the stream name and logical name, and set the PLC connection name (`<PLC ConnectionName>`) as the stream's **Group**, **Source Logical Name**, and **Origin Source**.
4. **Select & Subscribe / Unsubscribe Tags**:
   - Use **`Ctrl+Click`**, **`Shift+Click`**, **`Ctrl+Shift+Click`**, or click **Select All** to select one or more tags in the Read Tags table.
   - Click **Subscribe Selected** to subscribe the selected tags (or **Unsubscribe Selected** to unsubscribe only the selected tags, or **Clear Subscriptions** to unsubscribe all).
   - Subscribed rows are visually highlighted with a warm orange theme background (`#3d2b0d` with `#ffd280` text) and live values immediately begin updating in the **Live Value** column.
5. **Start DX+ Transmit**:
   - In the **DXNet Transmit Settings (PLC -> DX+)** panel (top right), verify the responder **Port** (default **`27136`**).
   - Click **Start DX+ Transmit**.
   - The **Primary URI** updates to `dxnet://127.0.0.1:27136`, the `Read (PLC -> DX+)` status dot turns green, and `TX: <published> / <received>` increments in real time.

---

## 6. Write Mode: Writing DX+ Streams to PLC Tags (`DX+ -> PLC`)

1. Switch the header toggle to **Write (DX+ -> PLC)**.
2. **Connect to DXNet Transmitter(s)**:
   - In the **DXNet Receive Settings (DX+ -> PLC)** panel (top right):
     - Click **Discover** to find active DXPNet transmitters on the network via mDNS, **OR**
     - Enter the transmitter **Host** (default `127.0.0.1`) and **Port** (default `27126`) and click **+ Add**.
   - The transmitter appears in the connections table showing its `URI` (`dxnet://127.0.0.1:27126`), `Name`, and discovered `Streams` count.
3. **Map a Writable PLC Tag**:
   - In the **Writable ControlLogix Tags** table (bottom), locate the target PLC tag (filterable by **Search**, **PLC Filter**, and **Mapped Only**).
   - **Double-click the tag row** (or select the row and click **Map Selected Tag...**) to open the **Configure DXNet -> ControlLogix PLC Tag Mapping** dialog.
4. **Configure Source Stream & Transformations**:
   - **DXNet Source Stream**:
     - Select the **DXNet Connection**, **Source Logical Name**, and **Stream Logical Name** (or choose `Manual Topic / Long ID Entry` to type a wire Long ID directly).
   - **Vector / Array Reduction**:
     - When mapping a vector/waveform stream to a scalar PLC tag (or reducing an array), choose an operation:
       `Pass-Through (No Reduction)`, `Maximum (Max)`, `Minimum (Min)`, `Average (Mean)`, `RMS (Root Mean Square)`, `Peak-to-Peak (Max - Min)`, `Standard Deviation (Std Dev)`, `Median`, `Sum`, `First Element [0]`, `Last Element [-1]`, or `Element at Index [i]`.
   - **Value Transformation & Boolean Output**:
     - **Direct Value**: Writes the reduced value directly (coerced to the tag's CIP type).
     - **Linear Scale / Clamp**: Applies `Scale (m)`, `Offset (b)`, and optional `Clamp Min` / `Clamp Max` bounds.
     - **Boolean Condition (BOOL Output)**: Ideal for writing alarm trips, permissive interlocks (`PermitRun`), or window checks into `BOOL` PLC tags:
       - Configure primary comparison: `Value` `<op1>` `X` (where `<op1>` is `>`, `>=`, `<`, `<=`, `==`, or `!=`).
       - Optionally select `&& (AND)` or `|| (OR)` and a second comparison: `Value` `<op2>` `Y` (e.g., `Value > 10 && Value < 50`).
       - Set **Hysteresis / Deadband** to prevent chattering near threshold boundaries.
       - Check **Invert Output (!)** if you want `!(condition)`.
     - Verify the formula in the live **Expression Preview** banner.
   - **Write Rate Limiting**:
     - Set **Max Write Rate** in `Hz` (e.g., `10.00 Hz` = 100 ms minimum interval, or `0 Hz` for unlimited).
     - **Flush trailing value after rate window** (checked by default) ensures the most recent value is always flushed at the end of a burst.
     - **Write on value change only** skips redundant CIP writes if the output value has not changed.
   - Click **Save Mapping**.
5. **Start Inbound Bridge**:
   - You can toggle individual mappings on or off at any time using the **Active** checkbox in column 0 of the Writable Tags table.
   - Click **Start Inbound Bridge** in the **DXNet Receive Settings** panel.
   - The `Write (DX+ -> PLC)` status dot turns green, `RX` counter increments as DXPNet packets arrive, and the **Last Written** column displays the live values written to each ControlLogix tag.

---

## 7. Saving & Loading Configurations

- **Save Config**: Click **Save Config** in the header bar (or in the Writable Tags footer) to export a complete JSON configuration file (`dxnet_plc_bridge_config.json`, schema `dxnet-plc-bridge.v1`).
- **Load Config**: Click **Load Config** to restore all PLC connections, Read tag subscriptions (including per-tag Poll/COV modes, intervals, and deadbands), DXNet transmitter endpoints, and Write tag mappings with transforms.
- **Offline Resilience**: If a PLC hardware unit is temporarily offline when loading a configuration file, `DXNet Bridge (PLC)` still registers the PLC connection as `Offline` and preserves all saved Read subscriptions and Write mappings so they activate automatically once the PLC comes online and **Refresh Tags** is clicked.

---

## 8. Testing Locally Without PLC Hardware (`--test-server` & `--test-client`)

`DXNet Bridge (PLC)` includes a built-in, network-accessible **ControlLogix CIP / EtherNet/IP Test Server** and **Test Client** that speak the real Allen-Bradley EtherNet/IP & CIP wire protocol over localhost TCP/UDP sockets (`127.0.0.1:44818`).

### 8.1 Launching the ControlLogix CIP Test Server
Open a terminal and run:
```powershell
# From source:
py -3 apps/PLC/run_client.py --test-server

# Or from frozen executable:
dx+plc.exe --test-server
```
Optional CLI flags for `--test-server`:
- `--host 127.0.0.1` (default `127.0.0.1`)
- `--port 44818` (default `44818`)
- `--slot 0` (default CPU chassis slot `0`)
- `--model 1756-L75` (`1756-L75` for ControlLogix 5575 v33.11 or `1756-L63` for ControlLogix 5563 v20.19)
- `--channels <N>` (`0` loads the realistic **Turbine Demo** catalog with Controller & `Program:TurbineControl` tags, `STRING`, `TURBINE_STATUS` UDT, and `REAL[8]` vibration array; `N > 0` generates `N` uniform test channels)
- `--no-auto-start` (do not start the CIP listener automatically on launch)

**Using the Test Server GUI**:
- **Turbine Demo & Quick Presets**: Click **Turbine Demo** or preset count buttons (`10`, `25`, `50`, `100`, `250`, or **Advanced Setup...**) to populate tags and animated waveform generators (`Sine`, `Cosine`, `Square`, `Triangle`, `Sawtooth`, `Counter`, `Random Walk`, `Noise`).
- **Legacy 500B CIP Mode**: Check **Legacy 500B CIP Only** before starting the server to test 500-byte Forward Open fallback behavior.
- **Discovering & Connecting from DXNet Bridge (PLC)**:
  1. With `--test-server` running, open `DXNet Bridge (PLC)` and click **+ Add PLC**.
  2. Click **Discover...** — the test server (`127.0.0.1`, `1756-L75/A LOGIX5575`) is discovered via CIP `ListIdentity`.
  3. Click **Scan Backplane Slots...** to verify Slot 0 (`1756-L75/A LOGIX5575`) and Slot 1 (`1756-EN2T/D`).
  4. Click **Connect & Load Tags**.
- **Write Audit Log**: Any value written by `DXNet Bridge (PLC)` or `--test-client` appears immediately in the Test Server's **Write Audit Log** table with timestamp, client IP:port, scope, tag name, and value written, and automatically pauses that tag's waveform generator so your written value persists.

### 8.2 Launching the ControlLogix CIP Test Client
```powershell
# From source:
py -3 apps/PLC/run_client.py --test-client

# Or from frozen executable:
dx+plc.exe --test-client
```
Optional CLI flags for `--test-client`:
- `--ip 127.0.0.1:44818`
- `--slot 0`
- `--auto-connect`

**Using the Test Client GUI**:
1. Enter the target PLC `IP[:Port]` (or click **Discover...**) and `Slot`, then click **Connect**.
2. Browse the hierarchical **PLC Tag Explorer** tree grouped by scope (`Controller`, `Program:TurbineControl`).
3. Double-click tags (or click **Monitor Selected** / **Monitor All**) to poll live values, update counts, and effective poll rates (`Hz`).
4. Select any writable tag, enter a new value in **Write New Value**, and click **Write to PLC** to test direct CIP tag writes.
