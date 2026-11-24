# Gotchas

Pitfalls, one line each, with the fix and, where there is one, a link to the guide with details. Skim it before you start, and again when something stops making sense.

## Picking and unlocking

- **Marketing names can mislead.** "X8 Pro" and "X8 Pro Max" are different phones: check the codename with `fastboot getvar product`, not the marketing name. [`assess-your-phone.md`](guides/assess-your-phone.md)
- **A real-looking GitHub port may be for another codename.** Read the codename and SoC in the repo, not its name (the `klee` example). [`assess-your-phone.md`](guides/assess-your-phone.md)
- **A-only phones can't dual boot.** One system slot means Linux replaces Android. Check `slot-count` first.
- **MediaTek may have no BROM rescue.** A bad flash can be final; don't experiment without a recovery path. [`devices/xiaomi-poco-x8-pro-max.md`](../../../phones/xiaomi-dash/)
- **"Couldn't verify, sign in to your account again"** in Mi Unlock status: sign out, restart, sign back in, with mobile data on. [`guides/xiaomi-unlock-from-linux.md`](guides/xiaomi-unlock-from-linux.md)
- **The unlock may have no waiting period and wipes immediately.** Back up before you press Enter.
- **One unlock per account per year (reported).** Check the quota before planning a second unlock.

## Flashing

- **Never relock the bootloader** with non-stock software: it bricks the phone. [`guides/safe-flashing.md`](guides/safe-flashing.md)
- **Never run `flash_all_lock.sh`.** Run `flash_all.sh` only.
- **Never flash a sibling variant or another region's ROM.** `curtana`/`joyeuse`/`gram` are not interchangeable with `excalibur`.
- **Forget `-s <serial>` and you may hit the wrong device.** Put it on every fastboot write and erase.
- **Xiaomi's main ROM server is very slow**: use a mirror, then verify the checksum anyway.
- **The UBports installer won't downgrade Android for you**: flash the required stock Android first. [`guides/ubuntu-touch.md`](guides/ubuntu-touch.md)
- **"Partitioning" is off by default in the UBports installer**: turn it on when coming from Android, or 24.04 won't fit.

## postmarketOS build

- **PyPI `pmbootstrap` is stale (2023).** Clone 3.12.1 from git. [`guides/postmarketos-unsupported-variant.md`](guides/postmarketos-unsupported-variant.md)
- **pmaports is not recognised unless `origin` is `https://gitlab.postmarketos.org/postmarketOS/pmaports.git`.** Keep that canonical URL.
- **`~/.local/var/pmbootstrap/version` must contain `8`.** Write it by hand if `init` didn't.
- **`boot_size` below 512 is refused.** Patch `sanity_check_boot_size()`, use `PMB_ALLOW_SMALL_BOOT=1`, set `boot_size = 256`.
- **Plymouth (the boot splash) is reported to hang this family's display.** Add `plymouth.enable=0` to the device cmdline, bump `pkgrel`, update the checksum.
- **A device-package update can overwrite the local fix.** Keep the official package below your `pkgrel`; check `/boot/loader/entries/pmos.conf` after updates.
- **`excalibur`'s `hwname` matches no device tree.** Patch the placeholder's two `hwname=joyeuse` strings to `hwname=excalib`, same length.
- **The FIT is CRC-protected: don't patch it.** Change only the appended placeholder.
- **Never swap `boot` and `cache`.** `boot` is U-Boot, `cache` is the kernel.
- **The phone reboots slower than the network.** SSH may be refused for a minute after boot.

## Working remotely

- **A reinstall changes the phone's host key.** Use a dedicated `UserKnownHostsFile`. [`guides/working-from-your-pc.md`](guides/working-from-your-pc.md)
- **BusyBox `ip` takes `-o`, not `-br`.**
- **The user's terminal truncates long lines.** Put long commands in scripts; have the user run the short name.
- **Never store the phone password in a file.** Ask the user each session; the user runs `sudo` or shares the PIN for that session only.
- **`pkill -f`/`pgrep -f` can match and kill your own shell.** Run it in a separate command, or use a bracketed pattern like `[c]hromium`.
- **A GUI app started with `setsid` over SSH dies when the session ends.** Start it with `systemd-run --user --unit=… --collect` and pass `WAYLAND_DISPLAY` and `XDG_RUNTIME_DIR`. [`guides/working-from-your-pc.md`](guides/working-from-your-pc.md)
- **Port forwarding is off by default on postmarketOS.** Add `AllowTcpForwarding local` in `sshd_config.d`.
- **DevTools screenshots show only the page.** Use `grim` for the real screen. [`guides/chromium-kiosk.md`](guides/chromium-kiosk.md)

## Chromium and the kiosk

- **`--force-device-scale-factor` renders at 2.75× on Wayland**: scale in the compositor with `wlr-randr --scale 3` (whole number).
- **A normal browser window can't be under 500 CSS px.** Use `--app=<url>` with `--kiosk`.
- **The first-run dialog is wider than the screen.** Create `~/.config/chromium/First Run` or pass `--no-first-run`.
- **Chromium in Phosh overflows 360 px.** Set `scale-to-fit` globally with `gsettings` (old `sm.puri.phoc` schema, with the session DBUS address); the per-app switch leaves web-app windows cropped. [`guides/chromium-kiosk.md`](guides/chromium-kiosk.md)
- **`cage` has no on-screen keyboard.** Build one into the web UI.
- **`env(safe-area-inset-*)` is not filled here.** Keep your own top inset for the notch and corners.
- **`Atomic commit failed: Resource busy`** in `cage` is occasional page-flip contention, not a crash: expect some jank.
- **A killed Chromium leaves `Singleton*` locks.** Remove them before restarting.
- **No hardware video decode on Chromium/Alpine.** `vaInitialize failed` is expected; the GPU still draws.

## Modem, audio, camera, charging

- **Packaged `tqftpserv` 1.1.1 breaks the modem** (`readwrite` files stay 0 bytes, modem loops, `DeviceNotReady`). Use upstream ≥ 1.3 with a drop-in; remove it if the distro catches up. [`guides/qualcomm-modem-debugging.md`](guides/qualcomm-modem-debugging.md)
- **`online` won't work from a bad state.** Reset from `offline`, then wait (~25 s).
- **ModemManager can hold the modem in the wrong state.** Stop it, restart the remoteproc, try `qmicli` by hand to separate causes.
- **Don't chase the `qrtr0` MTU warning or `50-dns-filter.sh` failure.** They are harmless.
- **The speaker is quiet** because its amplifier stays at the default gain, and Linux has no speaker protection: don't raise the gain.
- **GNOME's "hum" test alert is too soft to judge the speaker.** Test with Firefox instead.
- **One camera sensor failing blocks them all.** `ov16a1q` failing stops `qcom-camss` registering; there is no usable camera. [`devices/xiaomi-redmi-note-9-pro-max.md`](../../../phones/xiaomi-excalibur/)
- **There is no mainline charger driver (SMB5).** Charge from a wall charger with the screen off; a PC port is near break-even. [`guides/charging-without-a-driver.md`](guides/charging-without-a-driver.md)
- **The battery gauge is unreliable.** Trust capacity and voltage over time; use `current_now` only for its sign (positive = drawn, negative = charging).
- **Call audio needs `q6voiced`, which isn't installed.** Calls connect but audio is unverified.

## Ubuntu Touch and ad blocking

- **The system is read-only except for a few bind-mounted files.** Write `/etc/hosts` with `sudo cp`; `install` fails with "Read-only file system". [`guides/ubuntu-touch.md`](guides/ubuntu-touch.md)
- **24.04-1.x runs the top bar under the corners and camera hole.** Switch to the 24.04-2.x channel.
- **Big hosts lists miss networks.** Taboola's `tblcontent.com` was missing; find gaps by watching real requests, then add them.
- **The ad-block `update.sh` targets the old Ubuntu Touch host.** Change the target before reusing it on postmarketOS.
- **Screenshots need the phone unlocked.** If no new file appears, unlock and retry.

## Last verified

Checked 2026-11-21.
