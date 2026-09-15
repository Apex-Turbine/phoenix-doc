# PhoenixFoundry

`PhoenixFoundry` is an enterprise desktop application for inspecting, visualizing, maintaining, and converting high-rate industrial telemetry databases and file recordings across the Phoenix Ecosystem.

---

## 1. System Overview

PhoenixFoundry provides test engineers, data scientists, and operators with a unified workbench for managing multi-gigabyte recordings captured by Phoenix DAQ systems:

```
+-----------------------------------------------------------------------------------+
|                                  PhoenixFoundry                                   |
|                                                                                   |
|  [Database Adapters: DXDB / Postgres] <---> [PhoenixFacade] <---> [File Adapters] |
|                                                    |                              |
|           +----------------------------------------+                              |
|           v                                        v                              |
|  [PyQtGraph Decimator & ROI]              [Multi-Format Exporter]                 |
|  - 32,768 Envelope Bins                   - Strict Columnar CSV (timestamp_irig)  |
|  - Interactive Event Markers              - DATX & RWX Partitioning Containers   |
|  - Human-Readable UTC & IRIG Tooltips     - Database-to-Database Cloning          |
|  - Microsecond Selection                  - Downsampled Statistics & Reductions   |
+-----------------------------------------------------------------------------------+
```

### Core Capabilities
1. **Inspection & Stream Cataloging**:
   - Auto-discovers physical datasources, signal channels, inter-sample intervals (`delta`), and engineering units (`psi`, `degC`, `V`, `g`, etc.).
   - Visualizes hierarchical partition tables (`APEX_DATA`, `DS_DATA`) and discrete event markers (`APEX_DATAPOINT`, `DS_EVENT`).
   - Displays all record and event boundaries in human-readable UTC and IRIG date-time formats across the sidebar, info panel, and dialogs.
2. **High-Performance Waveform Visualization**:
   - Two-pass memory-bounded min/max envelope decimation algorithm capable of rendering 10,000,000+ data points smoothly in `pyqtgraph`.
   - Interactive Region of Interest (ROI) selection with nanosecond-accurate bounds for targeted exports and analysis.
3. **Strict Columnar CSV Export (BUG-3 & Timestamp Standard)**:
   - Exports multi-channel datasets conforming strictly to standard columnar layout: single primary time column (`timestamp_irig` by default in IRIG standard format, `timestamp_utc`, or `timestamp_ns`) followed by channel sample values.
   - Supports Full Rate and Downsampled output with statistical reductions (`Average`, `Min`, `Max`, `Peak-to-Peak`, `RMS`).
4. **Binary Container Export & Conversion**:
   - Native export to DATX and RWX file containers with flexible partitioning (`Single File`, `Split by Stream`, `Split by Source`, `Split by Time`).
5. **Database Engineering & Maintenance**:
   - Apply global or channel-specific time offsets (Timezone offsets or custom second/millisecond adjustments).
   - Sync record create/complete boundaries to actual raw sample extrema.
   - Reduce recording data strictly to active event ranges, permanently reclaiming disk space.
   - Consolidate and merge overlapping records within a database or across multiple physical database archives.
   - Built-in SQL console for ad-hoc inspection and maintenance queries.

---

## 2. Supported Adapters and Formats

PhoenixFoundry uses native dynamic adapters identified by canonical UUIDs matching the Phoenix C++ Core registry:

### 2.1 Database Adapters

| Adapter | Native Identifier | Canonical UUID | Extensions | Role |
| :--- | :--- | :--- | :--- | :--- |
| **DXDB / SQLite Reader** | `DXDBReader` | `d80bbdaa-93c4-4282-bacf-d78c9f8299e3` | `.dxdb`, `.db`, `.sqlite` | Reads SQLite-backed Phoenix recordings. |
| **DXDB / SQLite Writer** | `DXDBWriter` | `ce9112b6-d80f-466c-9562-9f1d9541c023` | `.dxdb`, `.db`, `.sqlite` | Creates or writes SQLite-backed Phoenix databases. |
| **PostgreSQL Reader** | `PostgreSQL Database Reader` | `b233c946-c454-4628-8695-6c9eaca91f5e` | URI / Network | Connects to central enterprise PostgreSQL stores. |
| **PostgreSQL Writer** | `PsqlDBWriter` | `8146bc04-cc35-4fa1-ab04-38ff17b7efdc` | URI / Network | Ingests records into enterprise PostgreSQL stores. |

### 2.2 File Adapters

| Adapter | Native Identifier | Canonical UUID | Extensions | Description |
| :--- | :--- | :--- | :--- | :--- |
| **RWX File Reader** | `RwxReader` | `36ccabd4-4c34-427e-950e-c3735bc0e5eb` | `.rwx` | High-throughput binary time-series file container. |
| **RWX File Writer** | `RWXWriter` | `cda19df1-084a-476e-973a-46c4cf7f694b` | `.rwx` | Serializes records into single or multi-part RWX files. |
| **DATX File Reader** | `DatxReader` | `8f3b2c1d-4e5a-4b6c-7d8e-9f0a1b2c3d4e` | `.datx` | Standard industrial binary container. |
| **DATX File Writer** | `DatxWriter` | `2e9adef9-dc25-4d01-89e5-a8286a8a82e8` | `.datx` | Serializes records into single or multi-part DATX files. |

---

## 3. Operator Guide

### 3.1 Launching the Application
Launch PhoenixFoundry from the repository root:
```powershell
python apps/PhoenixFoundry/src/main.py
```

Upon startup, the application verifies its license via `PhoenixLic` for the `FOUNDRY-APP` feature and initializes all native database and file adapters.

---

### 3.2 Connecting to a Database
1. Click **Connect DB** on the top ribbon.
2. In the **Connect to Database** dialog:
   - Choose the **Database Adapter** (e.g. `DXDB / SQLite Reader` or `PostgreSQL Database Reader`).
   - For file databases, click **Browse...** to pick a `.dxdb` file.
   - For DBMS databases, specify the connection URI and required authentication parameters (host, port, user, password, database).
3. Click **Connect**.
4. The database is added to the active session dropdown, and the sidebar populates with all discovered records.

---

### 3.3 Visualizing Time-Series Data & Interactive Range Selection
1. Select a **Record** in the sidebar. The **Overview** panel displays high-level metadata (start/end timestamps, channel counts, active tables).
2. Switch to the **Plot** view.
3. In the top selection bar:
   - Select the **Source** (e.g. `EngineSensors`).
   - Select the **Stream** (e.g. `CombustionChamberPressure`).
4. Click **Plot Stream**.
5. PhoenixFoundry loads and renders the decimated envelope instantly.
6. **Interacting with the Plot**:
   - **Pan / Zoom**: Left-click and drag to pan; right-click and drag or mouse wheel to zoom.
   - **Selection Window (ROI)**: Drag the highlighted region boundaries to select a specific time interval. The nanosecond start/end timestamps update automatically in the selection controls.
   - **Event Markers**: Toggle **Show Events** to overlay vertical event markers with event labels (`E1>`, `<E1`).
   - **Add Event**: Click **Create Event from ROI** to permanently tag the highlighted time window with a title and description.

---

### 3.4 Exporting to Columnar CSV
1. Click **Export CSV** on the ribbon or inside the export menu.
2. Select the **Target Record** and desired **Range Mode**:
   - **Full Record**: Exports the complete duration.
   - **Event Range**: Clips data strictly to the selected event boundaries.
   - **Time Range (ROI)**: Clips data strictly to the active plot selection.
   - **Custom Time Range**: Specify manual UTC start/end date-times (defaulted to record boundaries).
3. Select the **Time Format**:
   - **IRIG** *(Default)*: Canonical Inter-Range Instrumentation Group format `YYYY:DDD:HH:MM:SS.fffffffff` (where `DDD` is 1-based day of year, 001–366).
   - **IRIG (Day of Year)**: `DDD:HH:MM:SS.fffffffff` (day-of-year without leading year).
   - **ISO 8601 (UTC)**: Human-readable calendar format `YYYY-MM-DD HH:MM:SS.fff`.
   - **Unix Epoch (ns)**: Raw integer nanoseconds since 1970-01-01 00:00:00 UTC.
4. Select the **Export Mode**:
   - **Full Rate**: Outputs all raw samples at the original acquisition sample rate.
   - **Downsampled**: Bins samples into fixed time increments (e.g. `0.1` seconds for 100 ms bins).
5. When Downsampled is active, choose the **Statistic**:
   - `Average` (arithmetic mean)
   - `Min` (minimum value per bin)
   - `Max` (maximum value per bin)
   - `Peak-to-Peak` (difference between max and min)
   - `RMS` (root mean square)
6. Choose an export destination file path and click **Export**.
7. **Columnar Output Guarantee (BUG-3)**: The resulting file always contains exactly one time column (matching the selected time format, e.g. `timestamp_irig`), followed by each channel and its data:
   ```csv
   timestamp_irig,"EngineSensors.CombustionChamberPressure","EngineSensors.Temperature"
   2026:258:01:06:20.000000000,45.2,180.4
   2026:258:01:06:20.010000000,45.8,180.6
   ```

---

### 3.5 Exporting to Binary Formats (DATX, RWX, DXDB)
1. To export to binary files (DATX / RWX):
   - Click **Export File** on the ribbon.
   - Choose the file format adapter (`DATX File Writer` or `RWX File Writer`).
   - Select the **Partitioning Mode**:
     - `Single File`: Creates a unified container.
     - `Split by Stream`: Generates separate files per signal stream.
     - `Split by Source`: Generates separate files per physical datasource.
     - `Split by Time`: Chunks data into discrete time-windowed files.
2. To export to another DXDB database:
   - Click **Export DXDB** on the ribbon.
   - Specify the target `.dxdb` file path, record ID, and partitioning mode.

---

### 3.6 Database Management & Maintenance Tools
Click **Database Tools** on the ribbon to open the maintenance suite:

1. **Database Maintenance**:
   - **Vacuum**: Rebuilds the database file, defragmenting pages and shrinking file size.
   - **Optimize**: Analyzes indexes and updates query planner statistics for optimal read throughput.
2. **Time Offset Adjustment**:
   - Select **Timezone Offset** to shift all timestamps by a fixed UTC offset (e.g. UTC-05:00).
   - Select **Custom Offset** to shift timestamps by exact seconds, milliseconds, or microseconds.
   - Choose the scope: Whole record, specific source, or specific stream.
3. **Record Bounds Synchronization**:
   - Click **Sync Record Create/Complete Bounds** to scan the raw data vectors and update `create_time` and `complete_time` in `DS_INDEX` to exact sample minimum and maximum timestamps.
4. **Data Reduction to Event Ranges**:
   - Click **Reduce Data To Event Ranges** to purge all raw data samples occurring outside defined events. A safety confirmation dialog prompts before deletion.
5. **Record Deletion**:
   - Click **Delete Record + Related Data** to permanently delete a record, its index entry, event points, and table partitions. A safety confirmation dialog prompts before deletion.
6. **Merging Records**:
   - **Same-Database Merge**: Specify start/end times and click **Merge Matching Records** to combine overlapping records into a single consolidated record.
   - **Cross-Database Merge**: Click **Select Source DBs...**, specify a new **Destination URI**, select the **Destination Writer**, and click **Merge Across Selected Databases** to synthesize a new consolidated master database.
7. **Custom SQL Console**:
   - Enter arbitrary SQL queries directly against the connected database to inspect custom tables or run diagnostic checks.

---

## 4. Verification and Testing

PhoenixFoundry includes a comprehensive automated test suite validating all core functions:

```powershell
pytest apps/PhoenixFoundry/tests -v
```

Tests run offline using synthetic temporary DXDB databases and verify:
- Adapter canonical UUID resolution (BUG-2).
- Columnar CSV formatting and downsampling reductions (BUG-3).
- Fast aligned and slow cursor stream synchronization.
- Binary file export (DATX, RWX, DXDB).
- Database bounds sync, offset operations, cascade deletions, and license enforcement.
