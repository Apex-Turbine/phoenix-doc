# Licensing from Python (`PhoenixLic` & `apexlmclient`)

Before instantiating licensed acquisition hardware (`Device`), DSP processor components, or database writers in Python, initialize **`PhoenixLic`** (`PhoenixLic.i` / `apexlmclient.i`) against your running **APEX License Manager (`ApexLM`)** instance.

> [!NOTE]
> Make sure the local ApexLM daemon is reachable on CurveZMQ port `27884` (`APEXLM_PORT`), or configured to reach a floating server (`27885` / `27886`). You can check active seats and Ed25519 licenses in your browser at `http://127.0.0.1:27887/`.

## Startup & Checkout Pattern

```python
from phoenixpy import PhoenixLic, apexlmclient

lic = PhoenixLic.PhoenixLic.instance()

# 1. Configure application metadata (DEFAULT_APP_ID = "7e2c9a45-0d61-4f38-95b2-6c840e3a7f16")
app_id = apexlmclient.ApexLmClient.DEFAULT_APP_ID
lic.setAppInfo(app_id, "PHOENIX", "1.0.0", "localhost", "127.0.0.1", 0)

# 2. Connect over CurveZMQ (returns bool + error string depending on SWIG typemap)
res = lic.connect()
print("Connect result:", res)

# 3. Check out required feature(s) before starting licensed components/devices
checkout_res = lic.checkoutFeature(app_id, "ANALYSIS", 1)
print("Checkout result:", checkout_res)

# ... run pipeline / devices / file & database work ...

# 4. Clean check-in and disconnect on shutdown
lic.checkinFeature(app_id, "ANALYSIS", 1)
lic.disconnect()
```

> [!TIP]
> Hardware drivers (`Device::initialize()`) automatically handle `checkoutHardware(vendor, serial, err)` for connected chassis serial numbers and release them cleanly during `uninitialize()`.

See [C++ Licensing Guide](../C++/licensing.md) and [PhoenixCore PhoenixLic Reference](../PhoenixCore/PhoenixLic.md) for port numbers (`27884`–`27888`), borrowing behavior (`30 days`), and registered `ApexApps.csv` application names.
