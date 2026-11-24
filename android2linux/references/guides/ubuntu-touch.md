# Ubuntu Touch

Installing Ubuntu Touch with the UBports Installer, reaching the phone from your PC, and living with its read-only system.

## Prerequisite: the right Android base

Ubuntu Touch on this family runs over **Android 10's** hardware drivers through Halium (Halium 10). The installer does **not** downgrade Android for you. If the phone is on a newer Android, flash the appropriate stock Android 10 first (see [`safe-flashing.md`](safe-flashing.md)) or the install will not have the vendor base it expects.

The reference install is Ubuntu Touch **24.04-2.2**: Halium 10 over Android 10 vendor drivers, the Lomiri shell, and Qt WebEngine for the browser (Chromium 140).

## Install with the UBports installer

Use the **UBports Installer 0.11.2** AppImage, checksum verified, with the phone in **fastboot**. It recognises the `miatoll` family directly.

In the installer's options, set:

- **Channel:** `24.04` stable.
- **Wipe Userdata:** on.
- **Partitioning:** **on.** It is off by default, and you need it when coming from Android: it grows the system partition to about 3.5 GB so 24.04 fits.
- **Bootstrap:** on (the default).

The installer downloads about 1 GB, flashes, and the phone boots into Ubuntu Touch. Display, touch, GPU, Wi-Fi and mobile data work; UBports' device page also lists calls and the camera (treat calls and camera as untested until you exercise them).

## Developer Mode and SSH

1. Set a **passcode**.
2. Settings → About → **Developer Mode** → on.
3. With USB connected, `adb shell` now logs in as the user `phablet`.

To work over SSH (key-only logins by default), push your public key and start the server:

```sh
adb shell 'mkdir -p ~/.ssh && chmod 700 ~/.ssh'
adb push ~/.ssh/id_rsa.pub /home/phablet/.ssh/authorized_keys
adb shell -t sudo systemctl enable --now ssh     # asks for the phone passcode
ssh phablet@<phone-ip>
```

Once SSH works, you can drop `adb` for most tasks. See [`working-from-your-pc.md`](working-from-your-pc.md).

## The read-only system, and what you can still change

Ubuntu Touch mounts the system read-only. A small number of files are writable bind mounts, and `/etc/hosts` is one of them. Edit them **in place** rather than replacing them.

`install` fails with **"Read-only file system"** even on those files. Write them with `sudo cp` instead:

```sh
sudo cp /tmp/hosts.new /etc/hosts
```

Apps run under **AppArmor**, and background apps are **SIGSTOPped** when they are not in front: a frozen app does not keep running in the background the way Android or desktop Linux would. Updates go through `system-image-cli`.

## 24.04-1.x versus 24.04-2.x: the cutouts

The phone's device profile describes its rounded corners and camera hole, but the Lomiri shell in the **24.04-1.x** series ignores them: the clock and status icons run under the corners and the camera hole. Cutout support landed in **24.04-2.x**, so switching channel fixes it with no hacks:

```sh
ssh -t phone sudo system-image-cli -v --switch 24.04-2.x/arm64/android9plus/stable
```

The update keeps data and takes about ten minutes. After it, the top bar grows to make room for the camera, with the icons split around it. If you install 2.x directly, this is already done.

## Screenshots from the PC

To take a screenshot from the PC, inject the Volume Up + Volume Down combination into the phone's input devices and copy the newest file out of `~/Pictures/Screenshots/`: [`screenshot --ubuntu-touch <host>`](../../scripts/screenshot) does both, finding the right input devices by their key capabilities (their numbers differ between phones). The phone must be unlocked. On postmarketOS, `screenshot <host>` uses `grim` instead.

## Hosts-file ad blocking

Because `/etc/hosts` is writable, you can block ads system-wide by merging the **StevenBlack** hosts list (about 76k domains) with the phone's own entries and your own additions, then copying the result into place.

A big list is a good start, not exhaustive: Taboola's `tblcontent.com`, for example, is not in it and shows up while watching a site's requests over Chrome DevTools from the PC. Add domains like `0.0.0.0 tblcontent.com` (and its subdomains) to the custom entries to close the gap. Watch real traffic to find the rest.

Keep the merge in a script and copy the result with `sudo cp`:

```sh
# build hosts.merged = original + StevenBlack + custom, then
scp hosts.merged phone:/tmp/hosts.adblock
ssh -t phone 'sudo cp /tmp/hosts.adblock /etc/hosts'
```

This works on Ubuntu Touch. On Ubuntu Touch the browser's requests are visible through Morph's DevTools (below).

## Browser and Chromium

The default browser is **Morph**, whose engine is **Qt WebEngine** (Chromium 140). Only that built-in browser gets GPU acceleration, because Ubuntu Touch draws through Android's GPU drivers. To inspect a page, Morph can expose Chromium remote debugging via `QTWEBENGINE_REMOTE_DEBUGGING`. Relaunch Morph with that set and tunnel the port to the PC:

```sh
systemctl --user set-environment QTWEBENGINE_REMOTE_DEBUGGING=127.0.0.1:9222
```

Then `http://127.0.0.1:9222/json` lists pages and Chrome's `chrome://inspect` attaches. This is also how you find domains to block.

## Desktop apps: Libertine and snaps

Ubuntu Touch can run desktop applications in containers (Libertine) and as snaps. Neither is verified here; if you need desktop apps, check the current UBports documentation for your release.

## Last verified

Install, SSH, cutout switch, hosts ad blocking and Morph DevTools: 2026-11-20 on Ubuntu Touch 24.04-2.2. Checked 2026-11-21. Libertine/snaps: not tested.
