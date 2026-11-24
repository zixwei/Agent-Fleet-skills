# postmarketOS on an unsupported variant

How to install postmarketOS on a phone whose family is supported but whose exact variant isn't: build the image with pmbootstrap, make the bootloader pick the closest device tree, and flash. The Redmi Note 9 Pro Max ([`phones/xiaomi-excalibur`](../../../../phones/xiaomi-excalibur/)) is the example throughout. Read [Assess your phone](assess-your-phone.md) first, and [Safe flashing](safe-flashing.md) before you flash.

This is not a beginner path. Expect to reason about the boot chain rather than follow steps blindly.

## Reading the `deviceinfo` first

A postmarketOS device package lives in `pmaports` under `device/<category>/device-<vendor>-<codename>/`. Read its **`deviceinfo`** and `APKBUILD` before anything else: they give the flash method, the partition layout and the kernel package. Then check which device trees that kernel actually builds (in the kernel source, `arch/arm64/boot/dts/qcom/Makefile`). Together they tell you what the port expects and how close your variant is.

For `miatoll`, the package is `device-xiaomi-miatoll`, and it covers the family rather than one phone. Whichever variant you have, check that its device tree is actually in that list: the kernel builds `curtana` and `joyeuse` trees (one per panel vendor, Huaxing or Tianma), and none for `excalibur`. That absence is the reason for everything below.

Also read what your phone's own bootloader tells the kernel. On Android or Ubuntu Touch, `/proc/cmdline` contains the hardware name and the panel, for example `androidboot.hwname=excalibur` and `msm_drm.dsi_display0=dsi_ft8756_huaxing_vid_display`. You need both below.

## Build with `pmbootstrap` from git, not PyPI

The `pmbootstrap` on PyPI is from 2023 and too old for current pmaports. Clone it from git and link it:

- Clone `pmbootstrap` **3.12.1** from `gitlab.nura.eco/nura/pmbootstrap` (postmarketOS was renamed Nura in 2026; the tool and `postmarketos-*` package names are unchanged).
- Link it as `~/.local/bin/pmbootstrap`.

## Config tweaks

A few settings that `pmbootstrap init` would normally write need to be in place, plus a patch for the small boot partition.

**The small-boot-partition patch.** On this family the kernel boot image goes into `cache`, which is 384 MiB: smaller than pmbootstrap insists on. Patch `pmb/install/_install.py`, function `sanity_check_boot_size()`, so that with `PMB_ALLOW_SMALL_BOOT=1` a `boot_size` below 512 is allowed ([`pmbootstrap-small-boot.patch`](../../scripts/pmbootstrap-small-boot.patch)). Then set `boot_size = 256` (`pmbootstrap config boot_size 256`).

**The config file** `~/.config/pmbootstrap_v3.cfg` holds:

| Key | Value |
|---|---|
| `device` | `xiaomi-miatoll` |
| `ui` | `phosh` |
| `boot_size` | `256` |
| hostname | `redmi` |
| SSH key | your `~/.ssh/id_rsa.pub` |
| timezone | e.g. `America/Sao_Paulo` |
| extra package | `openssh-server` |

Write it with pmbootstrap's own `Config` and `pmb.config.file.save`, not with `pmbootstrap init`.

**The work folder** `~/.local/var/pmbootstrap/`:

- `version` must contain `8` (write it by hand, as `init` would).
- `cache_git/pmaports` is a checkout of the **v26.06** branch. Its `origin` **must** be `https://gitlab.postmarketos.org/postmarketOS/pmaports.git`: that is the URL pmbootstrap uses to recognise pmaports. It redirects to Nura; keep the canonical URL in `origin`.

## The local pmaports change

The device package needs one cmdline addition that the official package lacks:

- In `device/testing/device-xiaomi-miatoll/kernel-cmdline.conf`, add **`plymouth.enable=0`**. Plymouth (the boot splash) is reported to hang on this family's display driver; turn it off before the first boot.
- Bump **`pkgrel` 1 → 2** and update the checksum in the `APKBUILD`. pmbootstrap builds this locally and its higher version wins over the official package.

Updates on the phone are safe **as long as** the official `device-xiaomi-miatoll` stays below `9-r2` (official v26.06 was `9-r1` on 2026-11-20): apk keeps the higher version. If an update ever replaces it, check that `plymouth.enable=0` is still in `/boot/loader/entries/pmos.conf`, or rebuild your package with a higher `pkgrel`.

## The boot chain

Know this before you flash, because the layout is unusual:

```
ABL (the phone's own bootloader, with fastboot)
  → U-Boot, in `boot`
    → systemd-boot, on the FAT32 boot partition in `cache`
      → kernel
  root filesystem in `userdata` (ext4, grows to the whole partition on first boot)
  `dtbo` erased
```

- `boot` holds **U-Boot**.
- `cache` holds the **kernel** (the FAT32 image with kernel, initramfs, `dtbs/`, systemd-boot and `loader/entries/pmos.conf`).
- `userdata` holds the **root filesystem**, ext4, which grows to fill the partition on first boot.
- `dtbo` is **erased**: this family does not use it under Linux.

**Never swap `boot` and `cache`.** To update only the kernel side, flash `cache`; to replace the system, flash `userdata`, which wipes the user's data.

## How U-Boot picks a device tree

The only U-Boot release for this family (`u-boot-sm7125.img` from `github.com/sm7125-mainline/u-boot`, release 2025-06-02, SHA-256 `6878802d…`) carries the four real device trees in a **FIT** with CRC32 checks, plus a **placeholder** device tree appended to its kernel section that ABL fills in and hands over.

U-Boot chooses a tree whose `/chosen/match/<name>` strings **all** appear in the command line ABL gives it: see `arch/arm/mach-snapdragon/of_match.c`. For this family those match strings are the **panel string** and **`androidboot.hwname=curtana|joyeuse`**.

This phone's ABL says **`androidboot.hwname=excalibur`**, so nothing matches. U-Boot then carries on with the empty placeholder, which describes no hardware, so it can't boot Linux.

## The same-length placeholder patch

Make the phone match the `joyeuse` tree:

- In the **appended placeholder** device tree (not the FIT), change the two `androidboot.hwname=joyeuse` strings at offsets `0x92077` and `0x920fb` to **`androidboot.hwname=excalib`**: a substring of `excalibur`, same length. The patch touches **14 bytes** in the placeholder (spanning offsets `0x91e67`–`0x9215f`).
- Leave the FIT copy alone. It is CRC-protected, and you do not need to touch it: the placeholder is what ABL fills and what U-Boot matches against.

Same length is required because you are overwriting bytes in place, not rebuilding the structure. With the patch, this phone matches `sm7125-xiaomi-joyeuse-huaxing`, and the Note 9S still matches `curtana` because its `hwname=curtana` string is untouched.

[`uboot-match-patch.py`](../../scripts/uboot-match-patch.py) does the whole thing: it finds the placeholder, lists the match rules, patches the strings, and simulates the match before and after:

```sh
scripts/uboot-match-patch.py u-boot-sm7125.img --from joyeuse --to excalib \
  --bootargs "$(cat cmdline-from-android.txt)" -o u-boot-sm7125-excalibur.img
```

The new value must be the same length as the old one and a prefix of your phone's real `hwname`, so U-Boot's substring check succeeds.

## Verify the patch by parsing and simulating

Do not trust a byte patch you haven't checked:

1. **Parse** both placeholders and confirm `/chosen/match` now lists the excalibur-compatible strings, with the string lengths unchanged.
2. **Simulate the match**: take the command line ABL would produce (containing this phone's panel string and `androidboot.hwname=excalibur`) and confirm that, under the same "all match strings appear as substrings" rule, it now selects the joyeuse tree.

If the simulated match selects the joyeuse tree and the Note 9S still selects curtana, the patch is doing what it should.

## Build

From the project directory, with the patch and config in place:

```sh
PMB_ALLOW_SMALL_BOOT=1 pmbootstrap install
```

This asks for the PC's sudo password and the phone user's password, so the **user runs it**. Outputs in `~/.local/var/pmbootstrap/chroot_native/home/pmos/rootfs/`:

- `xiaomi-miatoll-boot.img`: 256 MiB FAT32 with the kernel, initramfs, `dtbs/`, systemd-boot and `loader/entries/pmos.conf`.
- `xiaomi-miatoll-root.img`: the ext4 root filesystem.

Both are world-readable, so plain `fastboot` flashes them without root. Inspect the boot image with `mdir`/`mtype -i <boot.img>` (set `MTOOLS_SKIP_CHECK=1`).

The packages for this device are `device-xiaomi-miatoll-9-r2` (the local build) and `firmware-xiaomi-miatoll-1-r1`.

## Flash order

```sh
R=~/.local/var/pmbootstrap/chroot_native/home/pmos/rootfs
fastboot -s <serial> flash cache    $R/xiaomi-miatoll-boot.img
fastboot -s <serial> flash userdata $R/xiaomi-miatoll-root.img
fastboot -s <serial> erase dtbo
fastboot -s <serial> flash boot     path/to/u-boot-sm7125-excalibur.img
fastboot -s <serial> reboot
```

Kernel side into `cache`, system into `userdata`, erase `dtbo`, U-Boot into `boot`, then reboot. Nothing boots until all four are written; a different order works too. Put `-s <serial>` on every write (see [`safe-flashing.md`](safe-flashing.md)).

## First boot over USB networking

On first boot the phone comes up on **USB networking** at the postmarketOS default **`172.16.42.1`**; the PC gets `172.16.42.2` on an `enx…` interface. The network comes up seconds after boot, before the full system, so `ssh` may be refused for a minute after a restart. Give the root filesystem time to grow to fill `userdata` on that first boot.

## Updates afterwards

`sudo apk upgrade` (or the Software app) is safe while the official device package stays below your local `pkgrel`. Kernel updates rewrite the boot partition in `cache` by themselves; **U-Boot in `boot` is never touched** by an update, so the patch survives.

## Last verified

Built and installed 2026-11-20 on postmarketOS v26.06, kernel `linux-postmarketos-qcom-sm7125` 6.14.7 from `sm7125-mainline`, device tree `sm7125-xiaomi-joyeuse-huaxing`. Checked 2026-11-21. The patch constants and hashes above are from that image; a newer U-Boot release may move them.
