# Working on the phone from your PC

Reaching the phone from a Linux PC over SSH or adb, and running commands and apps on it reliably.

## SSH over USB networking

postmarketOS brings up USB networking at fixed addresses: the phone is **`172.16.42.1`**, the PC gets **`172.16.42.2`** on an `enx…` interface. It comes up **seconds after boot, before the full system**, so `ssh` may be refused for a minute after a restart: that is timing, not a fault.

```sh
ssh pmos
```

## SSH over Wi-Fi

On Wi-Fi the address comes from your router and can change. Find it from the USB connection:

```sh
ssh pmos "ip -4 -o addr show wlan0"
```

Then:

```sh
ssh -o UserKnownHostsFile=~/.ssh/known_hosts_pmos user@<phone-ip>
```

Wi-Fi is set up in Phosh's settings; **NetworkManager** (`nmcli`) manages it. BusyBox `ip` here takes `-o`, not `-br`.

## An `ssh` config that survives reinstalls

```
Host pmos
    HostName 172.16.42.1
    User user
    IdentityFile ~/.ssh/id_rsa
    UserKnownHostsFile ~/.ssh/known_hosts_pmos
```

Use a **dedicated known-hosts file** for the phone: a reinstall changes its host key, and a separate file means you can clear it without touching the keys of everything else. Key authentication means no password prompts; put your public key on the phone during install (the pmbootstrap config takes it).

## `adb` when you are still on Android or Ubuntu Touch

On Android, enable Developer options and USB debugging. On Ubuntu Touch, set a passcode and turn on Settings → About → Developer Mode; `adb shell` then logs in as `phablet`. See [`ubuntu-touch.md`](ubuntu-touch.md). Once SSH works you rarely need `adb` again, but it is the bridge before SSH exists.

## Handling `sudo`

`sudo` on the phone asks for the **phone user's password** (a 4-digit PIN under Phosh's lock screen). There are two acceptable patterns, and one hard rule:

- **Preferred:** the user runs the `sudo` command themselves (or runs a script that does).
- **If the user explicitly shares it for the session:** they may pipe it, e.g. the user tells the assistant to run `echo <pin> | sudo -S -p "" <cmd>` over SSH so work doesn't stall.
- **Never store the password in a file, script, config or log.** In each new session, **ask the user** for it again.

Commands the **user** has to type should be short, or wrapped in a script they run, because terminals and chat windows often **truncate long lines**, and they cannot copy a long command reliably. Put anything long in a script under `bin/` and have them run the short script name.

## Keep long commands in scripts

Two reasons: the truncation above, and quoting. A long one-liner over `ssh`, especially one that itself contains quotes for `sudo sh -c '…'`, is easy to break. Write it as a script, copy it over, run it. Keep helpers such as `kiosk-on`, `kiosk-off`, `modem-diag` and `modem-reset` as files for exactly this.

## The `pkill`/`pgrep` self-match trap

`pkill -f <pattern>` and `pgrep -f <pattern>` match against the **full command line of every process**, including the shell that is running the `pkill`/`pgrep` command itself if that pattern appears in it. So a command like:

```sh
ssh pmos "pkill -f 'chromium --kiosk'; ..."   # the ssh command line contains the pattern
```

can match and kill its own shell, dropping the connection. When you need to kill a pattern that also appears in your own command, run the `pkill` in a **separate** command from anything else, or use a pattern that doesn't match your invocation (for example a bracketed first character, `[c]hromium`). Kill SSH tunnels in their own command too, for the same reason. The bracket trick only protects the pattern itself: if *other* text in the same command also matches (for example `[c]hromium/chromium` while the same command runs `/usr/lib/chromium/chromium-launcher.sh`), the shell is still killed. Keep kills in a command of their own.

## Launching GUI apps over SSH

Start them as a **transient user service**, so they belong to your user session, not to the SSH login:

```sh
ssh -n pmos 'systemd-run --user --unit=myapp --collect \
  --setenv=WAYLAND_DISPLAY=wayland-0 --setenv=XDG_RUNTIME_DIR=/run/user/$(id -u) \
  chromium --ozone-platform=wayland …'
ssh -n pmos 'systemctl --user stop myapp'      # to stop it
```

Don't start them with `setsid`: `setsid -f app </dev/null >/tmp/log 2>&1` detaches the app from the terminal, but on systemd it still sits in the SSH session's process group, and **systemd stops that group when the session ends**. The app dies a few seconds after `ssh` returns (Chromium then logs "GPU process launch failed … isn't usable. Goodbye."). The same goes for loggers started that way. Pass `WAYLAND_DISPLAY` and `XDG_RUNTIME_DIR` explicitly, and for Chromium `--ozone-platform=wayland`: without them it may fall back to X11 and exit with "Missing X server".

## DevTools over a tunnel

To inspect a browser on the phone, forward its remote-debugging port to the PC:

```sh
ssh -f -N -L 127.0.0.1:9223:127.0.0.1:9222 pmos
```

Then open `http://127.0.0.1:9223/json` on the PC (or `chrome://inspect` in Chrome). This needs **`AllowTcpForwarding local`** on the phone; postmarketOS ships `AllowTcpForwarding no`, so add it in `/etc/ssh/sshd_config.d/60-local-forwarding.conf` and restart SSH.

## Fastboot by hand

Power off, unplug USB, hold **Volume Down** then **Power** until the bunny shows, then plug in. Over SSH, `ssh -t pmos 'sudo reboot bootloader'` should also work; treat it as plausible rather than verified. Flashing itself is in [`safe-flashing.md`](safe-flashing.md).

## Last verified

SSH over USB and Wi-Fi, tunnel forwarding, GUI launch as a user service and the `pkill` caveats: 2026-11-20 to 2026-11-21. `reboot bootloader` over SSH: not tried as of 2026-11-21.
