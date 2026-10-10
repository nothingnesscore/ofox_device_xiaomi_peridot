# OrangeFox device tree for peridot (POCO F6 / Redmi Turbo 3)

- Maintainer: nothingnesscore
- OrangeFox manifest: `OrangeFox16/sync --branch 16.0` (Android 16)
- Branch: `fox_16.0`
- Base: `khargosxh18/twrp_device_tree_peridot@16`, reconciled with
  `AzzyC/ofox_device_xiaomi_peridot@fox_16.0` and official
  `OrangeFox/device/peridot_new@fox_16.0` (API 34 kept to match official).

Credits: AzzyC, khargosxh18, OrangeFox team (see Lineage & Credits below).
## Lineage & Credits

In compliance with open-source attribution requirements, this tree's
provenance is documented here. Original file headers and copyright
notices have been preserved unmodified in every file.

- Base tree: [khargosxh18/twrp_device_tree_peridot](https://github.com/khargosxh18/twrp_device_tree_peridot),
  branch `16`, commit `5f8c0ae` ("peridot: High-Speed Flash", 2026-10-01).
  Provides BoardConfig (cortex-a76, sepolicy), full vendor blobs,
  `payload-dumper-go`, I/O-tuning `runatboot.sh`, `formatdata.sh` /
  `rebootsystem.sh`, flashlight, `FFiles/DFE.zip`, `high-speed.md`.
- Reconciled with: [AzzyC/ofox_device_xiaomi_peridot](https://github.com/AzzyC/ofox_device_xiaomi_peridot),
  branch `fox_16.0`, and the official
  [OrangeFox/device/peridot_new](https://gitlab.com/OrangeFox/device/peridot_new),
  branch `fox_16.0`, commit `48dffb0` ("peridot: allow restarting WiFi
  supplicant", 2026-09-19) — API 34 and partition layout kept to match
  the official tree.
- Auto-DFE logic derived from the `auto-dfe` branches of both GitHub
  trees (pre/post ROM-flash scripts, `beforereboot.sh` SELinux fix),
  reworked into the safer variant described above (no `make_f2fs`).
- This is a lineage rewrite (squashed history), not a GitHub fork, so
  upstream history is referenced by commit hash above rather than being
  embedded. All prebuilt binaries (`prebuilt/kernel`, `FFiles/DFE.zip`,
  `payload-dumper-go`, vendor firmware) are carried over unchanged from
  the base tree; their original sources are the upstream repos linked
  here.
- Licenses: build files (`.mk`) under Apache-2.0 (see file headers);
  `vendorsetup.sh` and Fox scripts under GPLv3 (see file headers);
  OrangeFox/TWRP base code belongs to their respective projects
  ([OrangeFox](https://gitlab.com/OrangeFox),
  [TeamWin](https://github.com/TeamWin), [OrangeFox16](https://github.com/OrangeFox16)).
## Features

1. Android 16 build (`fox_16.0`, `BOARD_RECOVERYIMAGE_PARTITION_SIZE=100M`,
   `erofs` dynamic partitions, FBEv2 decrypt, AIDL haptics/vibrator).
2. Safe Auto-DFE enabled (`FOX_VARIANT=auto-dfe`):
   - `pre_rom_flash.sh`: SPL reset, wipe `/metadata/ota` only
     (no `make_f2fs /metadata`), slot-aware recovery backup.
   - `post_rom_flash_completion.sh`: restore recovery to both slots,
     queue `install /FFiles/DFE.zip`, save log to `/persist/Fox/logs/`,
     `reboot recovery`. Ships `recovery/root/FFiles/DFE.zip`.
3. High-Speed Flashing (device-tree side):
   - `runatboot.sh optimize_io_and_cpu` (scheduler `none`,
     `read_ahead_kb 2048`, `nr_requests 512`, `walt` governor),
     touch reset, `prune_historic_logs.sh`.
   - `payload-dumper-go` for parallel payload extraction.
   - See `high-speed.md` for benchmarks.
   - Note: verified against khargosxh18's own builder
     (`Actions-Build-OrangeFox`), which uses the stock manifest with
     no bootable/vendor fork overrides — device-tree side is all
     that's needed. This tree therefore uses stock `OrangeFox16 16.0`.
## Extras kept from khargos:16

- `sepolicy/recovery/`, `formatdata.sh` marker, `rebootsystem.sh`
  post-format cleanup, `beforereboot.sh` SELinux fix,
  `peridot_flashlight.sh`, full touch + `cnss/wlan` modules.

## Build locally

```sh
git clone https://github.com/OrangeFox16/sync
./sync/orangefox_sync.sh --branch 16.0 --path ~/fox_16.0
cp -a <this-tree> ~/fox_16.0/device/xiaomi/peridot
cd ~/fox_16.0
source build/envsetup.sh
lunch twrp_peridot-bp2a-eng
mka adbd recoveryimage
```

Outputs: `out/target/product/peridot/recovery.img`,
`out/target/product/peridot/OrangeFox-*.zip`.

## GitHub Actions

`.github/workflows/build.yml` builds `recoveryimage` on
`ubuntu-22.04`, uploads `recovery.img` + `OrangeFox-*.zip` as
artifacts, and attaches them to tag releases.
