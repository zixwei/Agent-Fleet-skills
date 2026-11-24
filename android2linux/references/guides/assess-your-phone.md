# Assess whether your phone can run Linux

The first step for any phone: find out, before you unlock or flash anything, whether a Linux port exists for it and how complete it is. Everything here is read-only.

## Start with the codename, not the name

Every port list, device tree and installer identifies phones by their codename. Marketing names can be misleading: the Poco X8 Pro and the Poco X8 Pro Max are unrelated phones inside, and a port for one doesn't run on the other.

Read the codename from the phone itself. Turn on USB debugging (*Settings → About phone*, tap the build or OS version seven times, then *Developer options → USB debugging*), connect it to your PC and run:

```sh
adb shell getprop ro.product.device          # e.g. excalibur
adb shell getprop ro.build.version.incremental
adb reboot bootloader
fastboot getvar product    # should agree with the codename
fastboot getvar anti       # anti-rollback index; matters for downgrading
fastboot getvar unlocked   # yes/no
```

Use `ro.product.device` and `fastboot getvar product`, not a shop listing or the name on the box. `fastboot reboot` brings the phone back to Android.

## SoC vendor: Qualcomm versus MediaTek

The best predictor of whether a port exists is the chip, and within chips the vendor:

- **Qualcomm** recent SoCs, especially the `sm7125`/`sm8250`-era ones, have real mainline work behind them. The `miatoll` family (Redmi Note 9S / Note 9 Pro / Note 9 Pro Max / Poco M2 Pro) has Ubuntu Touch, postmarketOS, Droidian and Sailfish ports, so it is a feasible target: as on the Redmi Note 9 Pro Max ([`phones/xiaomi-excalibur`](../../../../phones/xiaomi-excalibur/)).
- **MediaTek** recent chips, and new flagships in particular, usually have **no** mainline support. The Poco X8 Pro Max ([`phones/xiaomi-dash`](../../../../phones/xiaomi-dash/), codename `dash`, Dimensity 9500s) has no port, and no public BROM rescue if a flash goes wrong.

A port can exist for one variant of a family and not another. The Redmi Note 9 Pro Max (`excalibur`) has no device tree of its own even though the family is supported; it runs on the Note 9 Pro's tree with a patch. So "the family is supported" is a starting point, not a guarantee: see [`postmarketos-unsupported-variant.md`](postmarketos-unsupported-variant.md).

## A/B versus A-only slots

- **A/B** phones keep two copies of the system and switch between them on update. You have a fallback slot and dual boot is more plausible.
- **A-only** phones have one system slot, so a Linux install **replaces** Android and there is no second slot to fall back to. The Redmi Note 9 Pro Max is A-only, so dual boot is not possible and the "ways back" matter.

Check with `adb shell getprop ro.boot.slot_suffix` (`_a` or `_b` on A/B phones, empty on A-only) or `fastboot getvar slot-count` (on many A-only phones this answers "Variable not found" rather than 1).

## Where to check for ports

Check all of these, and check them by **codename**:

- **This repository's [`phones/`](../../../../phones/)**: phones already done, with install steps and the fixes each needs.
- **postmarketOS / Nura wiki and pmaports**: the wiki is at `wiki.nura.eco` (postmarketOS was renamed Nura in 2026; the package names are still `postmarketos-*` and the tool is still `pmbootstrap`). The device list and each device's page tell you what works. The actual port lives in `pmaports`, under `device/<category>/device-<vendor>-<codename>/`. Look for a `deviceinfo` file and read what it enables.
- **Ubuntu Touch**: `devices.ubuntu-touch.io` lists ports and their install channels. The `miatoll` family has an official, maintained port on 24.04.
- **Droidian**: a Debian-based option; check its device list for your codename.
- **Sailfish OS**: check the Sailfish community port lists.

A device on none of these lists has no one maintaining a Linux port for it today, regardless of what a single GitHub repo claims.

## Unlock policy

A port is useless if you can't unlock the bootloader. Check this before anything else:

- **Xiaomi:** the unlock is a Xiaomi-account process, can involve a waiting period (the classic 168 hours) or none, and reportedly allows one unlock per account per year. A newer phone may only unlock through the Xiaomi Community app with a quota. See [`xiaomi-unlock-from-linux.md`](xiaomi-unlock-from-linux.md).
- Other vendors have their own policies; look them up per vendor rather than assuming.

If the bootloader can't be unlocked, or unlocking would spend a quota you need for another device, stop here.

## How to read a random GitHub port critically

Repos named after a phone are not proof of a port for that phone. Before you believe one:

1. Find the **codename** in the README, the device tree, or the installer config, and compare it to `fastboot getvar product`. A repo for `klee` does nothing for `dash`.
2. Check the **SoC** it targets. A port for a Dimensity 8500 does not run on a Dimensity 9500s.
3. Check the installer: if it refuses all codenames but one, that one is the device it's for.
4. Look at **recency** and **activity**: last commit, open issues, whether anyone reports it booting.
5. Read what does **not** work. A port that boots with no modem, no camera and no charging is still a port, but you should know that before you start.

A repo can be genuine and still be for another phone: `emir34-34/ubuntu-touch-poco-x8-pro` is a real port, with real instructions, for the Poco X8 Pro (`klee`), not the Poco X8 Pro Max ([`phones/xiaomi-dash`](../../../../phones/xiaomi-dash/)).

## If there is no port

Linux tools still run inside Android: Android 16's built-in Linux Terminal (a Debian virtual machine, in *Developer options*) or [Termux](https://termux.dev). For a Linux phone, choose a model from the lists above; [`phones/`](../../../../phones/) shows what to expect from the ones already done.

## A short checklist

- [ ] Codename confirmed from the phone, not the marketing name.
- [ ] SoC vendor known; no MediaTek flagship assumptions.
- [ ] Slot layout known (A/B or A-only).
- [ ] Port found on at least one real distro list, keyed by codename.
- [ ] Bootloader unlock policy checked and feasible.
- [ ] If the port is for a sibling variant, read [`postmarketos-unsupported-variant.md`](postmarketos-unsupported-variant.md) before committing.

## Last verified

Checked 2026-11-21.
