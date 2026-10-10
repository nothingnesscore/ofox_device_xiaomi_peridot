### ⚡ HIGH-SPEED FLASH: THE NEW WAY OF ROM FLASHING

* Bored of the slow, strict, and cryptic `update_engine`?
* High-Speed Flash is a custom-built, multithreaded OTA engine that fundamentally changes how you install ROMs on OrangeFox.

### 🌟 THE ADVANTAGES

* **Flashes in ~40-50 Seconds:** Uses 8 parallel background workers to extract and write multiple partitions at the exact same time.
* **Smart Super Partition Resizing:** We didn't bypass logical resizing—we perfected it. The engine rigorously checks the declared Super group size directly from the payload and resizes partitions perfectly, ensuring the ROM never fails due to dynamic space constraints.
* **Pre-Flight Safety Checks:** Before a single byte is ever written to your device, the engine runs strict sequential verifications on the package. If the ROM is fundamentally broken or incompatible, it aborts instantly *before* the flash even begins.
* **3-Layer Proper Security:** During the flash, your device is constantly protected by strict runtime checks. Our custom system traps hard OS crashes, sniffs the IPC stream in real-time for bad CRCs or silent panics, and enforces a strict 100% completion tripwire for every single partition.
* **100% Fail-Safe Fallback:** If a zip is corrupted or a download is broken, the engine instantly catches it and safely aborts. Your currently booted slot remains completely untouched. You can simply reboot to your previous system normally, or just flash using the standard method.
* **Zero Storage Overhead:** Streams data straight to the hardware block maps. It unpacks zero temporary files and requires absolute zero free space on your internal storage.
* **Plain English Errors:** No more Googling "Error 29" or "Error 7". If a zip is damaged, the recovery tells you exactly what went wrong in simple words.

### ⏱️ REAL-WORLD SPEED BENCHMARKS

| ROM | ❌ Standard Recovery | ✅ High-Speed Flash |
| :--- | :--- | :--- |
| **Stock HOS (HyperOS)** | ~330 seconds (5.5 mins) | **~60 seconds** |
| **ASCP** | ~244 seconds | **~48 seconds** |
| **InfinityX** | ~223 seconds | **~40 seconds** |
| **XPerience AOSP** | ~218 seconds | **~38 seconds** |

### ⚡ OTA ENGINE SHOWDOWN: OLD vs. HIGH-SPEED

| Feature | ❌ OLD: STANDARD UPDATE_ENGINE | ✅ NEW: HIGH-SPEED FLASH |
| :--- | :--- | :--- |
| **Metadata Verification** | Fails if strict ROM signatures or properties mismatch. | **[ BYPASSED ]** Skips strict meta-hashes and reads straight to data. |
| **Super Partition Setup** | Blindly attempts resize, often failing on space constraints. | **[ SMART ALLOCATION ]** Reads payload Super size upfront for perfect resize. |
| **Extraction Speed** | Sequential and slow (takes several minutes to finish). | **8-Core Parallel I/O** (Extracts 8 parts at once, finishes in 40-50 secs). |
| **Error Handling** | Cryptic error codes (Throws "Error 29" and leaves you guessing). | **Fail-Safe Protection** (Aborts safely, explains error, OS remains intact). |
