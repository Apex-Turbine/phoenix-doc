# Licensing from Python (`PhoenixLic` & `apexlmclient`)

> [!IMPORTANT]
> **End-User API & Format License Notice:** Phoenix Python (`phoenixpy`) APIs and APEX file/database/protocol formats are licensed strictly for **internal end-user use** and cannot be used for commercial means unless explicitly authorized by APEX through formal written agreement ([Software License Agreement](../../../Release%20Policies/software-license-agreement.md)).
>
> **Legacy FLEXlm / FlexNet Discontinuation (October 2027):** Legacy **FLEXlm / FlexNet** (`lmgrd`) licensing ends in **October 2027**. All product versions going forward use **[APEX License Manager](../../../License%20Manager/README.md)** (`ApexLM`).

Before instantiating licensed acquisition hardware (`Device`), DSP processor components, or database writers in Python, initialize **`PhoenixLic`** (`PhoenixLic.i` / `apexlmclient.i`) against your running **[APEX License Manager (`ApexLM`)](../../../License%20Manager/README.md)** instance.

> [!NOTE]
> Make sure the local ApexLM daemon is reachable on CurveZMQ port `27884` (`APEXLM_PORT`), or configured to reach a floating server (`27885` / `27886`). You can inspect active seats and load Format Version 2 licenses in your browser at `http://127.0.0.1:27887/`.

## Startup & Checkout Pattern

```python
from phoenixpy import PhoenixLic, apexlmclient

lic = PhoenixLic.PhoenixLic.instance()

# 1. Configure application metadata using ApexLmClient.DEFAULT_APP_ID
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

See [C++ Licensing Guide](../C++/licensing.md), [PhoenixCore PhoenixLic Reference](../PhoenixCore/PhoenixLic.md), **[APEX License Manager](../../../License%20Manager/README.md)**, and **[Web UI & Offline Reservations](../../../License%20Manager/web-ui-and-reservations.md)** for port numbers (`27884`–`27888`), offline reservation behavior (**Reserve** tab, up to `30 days`), and registered `ApexApps.csv` application names.
