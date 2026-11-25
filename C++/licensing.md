# C++ Developer Licensing (`PhoenixLic` & `ApexLmClient`)

Phoenix enforces component, stream, channel, token, and hardware serial entitlements through **`PhoenixLic`** (`PhoenixCore/PhoenixLic/PhoenixLic.hpp`) backed by **APEX License Manager (`ApexLM`)** (`PhoenixCore/PhoenixLic/apexlmclient.hpp`).

## ApexLM Protocol, Ports & Licensing Modes

- **Transport**: ZeroMQ **CurveZMQ** encrypted client/server messaging with **Ed25519**-signed JSON licenses stored in SQLite databases (`ApexLicenseManager.md`).
- **Deployment Modes**: `LOCAL`, `PASSTHROUGH`, and floating `SERVER` (including offline borrowing up to **30 days**).
- **Network Ports**:

| Port | Constant | Purpose |
| --- | --- | --- |
| `27884` | `APEXLM_PORT` | Local ApexLM CurveZMQ client handshake (`PhoenixLic::connect`) |
| `27885` | `APEXLM_SERVER_PORT` | Floating license server endpoint |
| `27886` | `APEXLM_SERVER_LISTEN` | Floating server listener |
| `27887` | `APEXLM_MANAGEMENT_PORT` | Local Web UI (`http://127.0.0.1:27887/`) |
| `27888` | `APEXLM_SERVER_MANAGEMENT_PORT` | Floating server Web UI |

## Registered Application Names & Identifiers

- **Default Phoenix Client App ID**: `ApexLmClient::DEFAULT_APP_ID` = `"7e2c9a45-0d61-4f38-95b2-6c840e3a7f16"`
- **ApexLM Registered Apps (`ApexApps.csv`)**: `PHOENIX`, `DXPLUS`, `DX+DAQ`, `DX+ONLINEANALYSIS`, `DX+OFFLINEANALYSIS`, `DX+REMOTEVIEWER`, `DX+DATAVIEWER`, `OPCUA`, `DBMANAGE`, `DS`, `DSAPI`, `ANY`.

## `PhoenixLic` Initialization & Feature Checkout Lifecycle

Use the `PhoenixLic::instance()` singleton early in application startup before initializing hardware devices or running component pipelines:

```cpp
#include <PhoenixCore/PhoenixLic/PhoenixLic.hpp>
#include <PhoenixCore/PhoenixLic/apexlmclient.hpp>
#include <iostream>

bool initPhoenixLicensing() {
    auto& lic = PhoenixLic::instance();
    std::string err;

    // 1. Register application identity (use DEFAULT_APP_ID for standalone Phoenix SDK)
    lic.setAppInfo(
        ApexLmClient::DEFAULT_APP_ID,
        "PHOENIX",
        "1.0.0",
        "localhost",
        "127.0.0.1",
        0
    );

    // 2. Connect over CurveZMQ to ApexLM (port 27884)
    if (!lic.connect(err)) {
        std::cerr << "ApexLM connect failed: " << err << "\n";
        return false;
    }

    // 3. Optional: inspect available license seats
    std::map<std::string, int> seats;
    lic.queryLicenseSeats("PHOENIX", seats, err);

    // 4. Check out required feature(s) (or pass a feature->count map)
    if (!lic.checkoutFeature(ApexLmClient::DEFAULT_APP_ID, "ANALYSIS", 1, err)) {
        std::cerr << "Feature checkout failed: " << err << "\n";
        lic.disconnect();
        return false;
    }

    return true;
}
```

At shutdown, release checked-out features/hardware and disconnect:
- `lic.checkinFeature(...)`
- `lic.checkinHardware(...)`
- `lic.disconnect()`

## Hardware Serial & Per-Component Licensing

- **Components (`Component.hpp`)**: Track required features in `m_featureMap` (including macros such as `LIC_CHANNELS_APEXDS` `"APEXDS_CHANNELS_"`, `LIC_CHANNELS_APEX` `"APEX_CHANNELS_"`, `LIC_ANALYIS` `"ANALYSIS"`) and store checked-out hardware vendor identifiers in `m_checkedOutHwVendor` for clean check-in.
- **Devices (`Device.hpp`)**: `Device::initialize()` automatically constructs and executes a hardware license checkout request (`Device::buildHardwareLicenseRequest`) against `checkoutHardware(vendor, serial, err)` using sanitized serials (`Device::cleanSerialNumber`), populates `getApprovedHardwareIdentifiers()`, and gates `isHardwareApproved(id)` and `isStreamLicensed(streamIdx)`. `Device::uninitialize()` stops active streams and automatically checks in hardware licenses.

See [PhoenixCore PhoenixLic Reference](../PhoenixCore/PhoenixLic.md) and [Ports Reference](../PhoenixCore/ports.md) for architecture details and troubleshooting.
