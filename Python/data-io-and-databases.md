# File, Database & Stream I/O in Python

Access recorded files (`.rwx`, `.datx`), SQLite & PostgreSQL historians, and live ZeroMQ / DS 3.x network streams directly from `phoenixpy`.

## 1. File I/O (`FileLibrary`, `FileReader`, `FileWriter`)

Load `"GetFiles"` plugins (`RwxReader`/`RWXWriter`, `DatxReader`/`DatxWriter`) through `FileLibrary.getFileLibraryManager()`:

```python
from phoenixpy import FileLibrary

file_mgr = FileLibrary.getFileLibraryManager()
# file_mgr.addPath("/path/to/bin/file")

for lib in file_mgr.getLibraries():
    for adapter in lib.getAdapters():
        print("Reader:", adapter.getReaderName(), "Writer:", adapter.getWriterName())
```

- Implement `FileReaderCallback` (SWIG director) or query readers created by `adapter.createReader()` to read historical blocks.
- For HDF5 exports (`APEX-H5`), configure an `HDF5Output` component inside a `GraphJson` pipeline ([`readme/outputs/apex-h5.md`](../readme/outputs/apex-h5.md)); standalone `File/HDF5` plugin classes are currently stubs.

## 2. Database I/O (`DatabaseLibrary`, `PhoenixDBConnection`, `DBQuery`)

Load `"GetDatabases"` plugins (`SQLite` `DXDBReader`/`DXDBWriter` and `PostgreSQL` `PostgreSQL Database Reader`/`PsqlDBWriter`) via `DatabaseLibrary.getDatabaseLibraryManager()`.

### Direct SQL & Binary Payload Queries (`DBQuery`)

When querying Phoenix SQLite or PostgreSQL databases directly with `PhoenixDBConnection` and `DBQuery`, use the verified `DBQuery` iteration methods (`step`, `columnInteger`, `columnDouble`, `columnText`, `columnBlob`, `execute`, `status`):

```python
from phoenixpy import DatabaseLibrary, DBQuery, PhoenixDBConnection

db_mgr = DatabaseLibrary.getDatabaseLibraryManager()
# db_mgr.addPath("/path/to/bin/database")

# Iterate rows returned by an executed DBQuery:
# query.execute()
# while query.step():
#     rec_id   = query.columnInteger(0)
#     val      = query.columnDouble(1)
#     name     = query.columnText(2)
#     raw_blob = query.columnBlob(3)
```

> [!NOTE]
> Use `step()` and `columnInteger` / `columnDouble` / `columnText` / `columnBlob` as implemented in `PhoenixCore/Database/DBQuery.hpp` and `swig/Database/DBQuery.i`.

## 3. Live Stream Reception (`StreamLibrary`, `StreamReceiver`)

Load `"GetStreams"` plugins (`DXPNetReceiver`/`DXPNetTransmitter` ZeroMQ and `DSNetReceiver` DS 3.x bridge) via `StreamLibrary.getStreamLibraryManager()`.

### Decoding `Message` Payloads into Python / NumPy

Incoming `Message` payloads carry a 30-byte header + typed binary payload (`Message.DATA_FLOATS` `0x04`, `DATA_DOUBLES` `0x05`, `DATA_CFLOATS` `0x06`, `DATA_CDOUBLES` `0x07`, `DATA_MESSAGES` `0x08` composite tree, etc.; see [`Message.md`](../PhoenixCore/Message.md)):

```python
import numpy as np
from phoenixpy import Message

def message_to_numpy(msg: Message.Message) -> np.ndarray | None:
    dt = msg.dataType()
    raw = msg.data()  # Binary payload bytes
    if dt == Message.Message.DATA_FLOATS:
        return np.frombuffer(raw, dtype=np.float32)
    if dt == Message.Message.DATA_DOUBLES:
        return np.frombuffer(raw, dtype=np.float64)
    if dt == Message.Message.DATA_CFLOATS:
        return np.frombuffer(raw, dtype=np.complex64)
    if dt == Message.Message.DATA_CDOUBLES:
        return np.frombuffer(raw, dtype=np.complex128)
    return None
```

Be mindful of `-DSWIG_PYTHON_STRICT_BYTE_CHAR` when working with raw `char*` buffer APIs vs `std::string` accessors on `Message`.
