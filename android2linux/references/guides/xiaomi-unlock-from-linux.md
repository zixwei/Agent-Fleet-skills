# Unlocking a Xiaomi bootloader from Linux

Unlocking a Xiaomi bootloader from a Linux PC, with no Windows step. Unlocking wipes the phone; read [Safe flashing](safe-flashing.md) first.

## What you need

- A Xiaomi phone you own, with a Mi Account signed in.
- `adb` and `fastboot` on the PC.
- A network connection on the phone.

## 1. Enable the developer settings

On the phone:

1. Settings → About phone → tap the **MIUI version** entry seven times to unlock Developer options.
2. Settings → Developer options → turn on **OEM unlocking** and **USB debugging**.
3. Connect USB and accept the debugging prompt on the phone; check `adb devices` shows it.

## 2. Bind Mi Unlock status

Developer options → **Mi Unlock status → Add account and device**. Do this with **Wi-Fi off and mobile data on**: the binding step is unreliable over some Wi-Fi networks.

If it fails with *"Couldn't verify, sign in to your account again"*:

1. Sign out of the Mi Account on the phone.
2. Restart the phone.
3. Sign back in, and repeat Add account and device with mobile data on.

See also the entry in [`../gotchas.md`](../gotchas.md).

## 3. MIUI versus HyperOS

The binding step is the same idea on both, but the menus and the waiting behaviour differ:

- **MIUI** (as on the Redmi Note 9 Pro Max, MIUI 14) uses the flow above directly.
- **HyperOS** (as on newer phones) may push you to the **Xiaomi Community app** for the unlock request instead of a simple Mi Unlock status binding. The quota resets at 00:00 Beijing time (13:00 in Brasília), and the wait is typically a week or more.

Xiaomi's **official** unlock tool is **Windows-only**, which is why the rest of this guide uses the community tool. The Community-app route cannot be replaced by the Linux tool; it is a separate gate.

Also be aware: Xiaomi reportedly allows **one unlock per account per year**. If the account has already unlocked a phone, the next one may be refused on quota, independent of any waiting period.

## 4. Unlock from Linux with MiUnlockTool

The community **MiUnlockTool** (`MiForge/MiUnlockTool`, published on PyPI as `miunlock`) talks only to Xiaomi's own servers. Its **browser-login** mode means you never type your Mi password into the tool itself: you sign in through a browser page it opens.

```sh
uv tool install miunlock==1.7.5
adb reboot bootloader
miunlock        # choose 1 (browser login), sign in, press Enter
```

The tool then performs the unlock. Keep the phone connected and in fastboot for the whole exchange.

## What to expect

- **A wait, or none.** The classic expectation is a 168-hour (7-day) wait after binding, but Xiaomi can answer **"Unlock successful"** straight away if the account has no wait.
- **It wipes the phone immediately.** Because there may be no waiting week to prepare, **back up before you press Enter**. You may not get a warning window.
- **Verify it worked.** After it reboots, `fastboot getvar unlocked` should report `yes`.

## After unlocking

- **Never relock** the bootloader once anything but stock MIUI is on the phone. Relocking with modified software is the classic way to brick it. See [`safe-flashing.md`](safe-flashing.md).
- You can now flash `boot`, `cache`, `userdata` and friends, install a custom recovery or a Linux distribution, and downgrade Android when a port requires an older vendor base (Ubuntu Touch on the miatoll family needs Android 10).

## Last verified

Performed 2026-11-20 with MiUnlockTool 1.7.5. Checked 2026-11-21. Xiaomi changes its unlock policy and quota over time; re-check the current process for your model and HyperOS version before relying on the steps here.
