# Safe flashing

How to flash without bricking the phone: read-only checks before any write, verifying a Xiaomi fastboot ROM, targeting one device, and the scripts and images you never use.

## Read-only checks first

Before flashing anything, ask the phone what it is and what state it's in. These commands don't write:

```sh
fastboot devices             # the serial of every phone in fastboot mode
fastboot getvar product      # the codename, e.g. excalibur
fastboot getvar anti         # anti-rollback index
fastboot getvar unlocked     # yes once unlocked
```

If `product` isn't the codename the image is for, stop.

## One device, by serial

Put `-s <serial>` on every `fastboot` command that writes, so only that phone is touched even if another one is connected or the phone re-enumerates:

```sh
fastboot -s <serial> flash boot <image>
fastboot -s <serial> erase dtbo
fastboot -s <serial> reboot
```

## Verify a Xiaomi fastboot ROM

[`check-rom.py`](../../scripts/check-rom.py) runs all of these checks on the archive or the unpacked folder:

```sh
scripts/check-rom.py excalibur_in_global_images_….tgz --device excalibur --anti 1
```

What it checks, and why:

1. **The archive's checksum.** The official archive name ends with the first 10 hex characters of its MD5 (`…_10.0_in_933f5d8da9.tgz`). A mismatch means a damaged download.
2. **Every image** against the ROM's own `md5sum.xml`. One bad image is reason enough to download again.
3. **The codename** in `flash_all.sh`'s first line, which refuses any other device.
4. **The anti-rollback index.** `flash_all.sh` declares `CURRENT_ANTI_VER`, but the script may not compare it with the phone's; compare it yourself. Never flash a ROM whose index is **lower** than the phone's (`fastboot getvar anti`).

Then run the plain script, with the serial:

```sh
bash flash_all.sh -s <serial>
```

## Never relock

- Never run **`flash_all_lock.sh`**, nor `fastboot flashing lock` or `oem lock`. Relocking with anything but the vendor's stock firmware on the phone bricks it. Leave the bootloader unlocked; going back to stock with `flash_all.sh` keeps it unlocked.
- Never flash an image for **another codename or region**, even a sibling: the Redmi Note 9 Pro Max (`excalibur`), Note 9S (`curtana`), Note 9 Pro (`joyeuse`) and Poco M2 Pro (`gram`) are one family, and their ROMs aren't interchangeable. A wrong-variant flash is a common way to hard-brick a phone.

Some families have a community recovery for hard bricks (for `miatoll`, reports describe `bkerler/edl` with a patched loader); recent MediaTek phones often have no public BROM rescue. Plan as if there were none.

## Downloading Xiaomi ROMs

Xiaomi serves the same files from several CDN hosts, at very different speeds: `bigota.d.miui.com` can be as slow as 20 KB/s, `bn.d.miui.com` about 25 MB/s. Keep the path and swap the host, then verify the file as above. [Xiaomi Firmware Updater](https://xmfirmwareupdater.com) lists the official ROMs by codename and version.

## Last verified

`check-rom.py` and the checks above on `excalibur_in_global_images_V12.0.4.0.QJXINXM` (Android 10): 79 images matched, `CURRENT_ANTI_VER=1`; 2026-11-21.
