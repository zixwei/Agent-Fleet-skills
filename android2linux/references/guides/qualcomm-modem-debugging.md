# Debugging a Qualcomm modem on Linux

How to get a Qualcomm modem online when Linux detects it but it won't register: narrow down the cause step by step. One cause, covered in full here, is an outdated `tqftpserv`. Commands run on the phone as root ([Working from your PC](working-from-your-pc.md)).

## 1. Confirm the basics

Before touching anything, confirm the SIM is seen and the card is usable, without printing identifiers. Over QMI as root on the phone:

```sh
qmicli -d qrtr://0 -p --uim-get-card-status
```

Look at `Card state`, `Application state`, `PIN1 state`, the slot list and personalization. A card that is absent, PIN-locked or not ready explains a lot before you go further.

Ask the modem for its operating mode and, through ModemManager, its operator, signal and access technology:

```sh
qmicli -d qrtr://0 -p --dms-get-operating-mode
mmcli -L
mmcli -m <n>              # state, power state, access tech, signal quality, operator
```

Bands and operator details come from the ModemManager modem object. If the card is fine and the mode is wrong, the section below is the next thing to understand.

## 2. Probe the operating mode

ModemManager's "couldn't enable the modem: … `DeviceNotReady`" (QMI error 52) only says the modem refused to switch its radio on. Ask the modem directly, and try the modes it offers:

```sh
qmicli -d qrtr://0 -p --dms-get-operating-mode
qmicli -d qrtr://0 -p --dms-set-operating-mode=low-power   # or offline, online, reset
```

The pattern to recognise is a modem that accepts every mode except `online`:

| Request | Result |
|---|---|
| (straight after boot) | already in `shutting-down`, with `HW restricted: no` |
| `low-power` | accepted |
| `offline` | accepted |
| `online` | refused: `DeviceNotReady` |
| `offline` → `low-power` | refused: `InvalidTransition` |
| `reset` (from `shutting-down` or `offline`) | accepted, but no actual restart: no restart in the kernel log, same mode afterwards |

A modem that accepts every mode except `online` thinks something it needs isn't ready, which points at what it depends on, not at the radio.

Since `reset` may do nothing, restart the modem's processor through the kernel instead, as root:

```sh
echo stop  > /sys/class/remoteproc/remoteproc0/state   # check .../name says "modem" first
echo start > /sys/class/remoteproc/remoteproc0/state
dmesg | grep -i remoteproc0
```

Watch `dmesg` for `remoteproc`, `qcom_q6v5`, `modem`, `sysmon`, `crash`, `fatal`. A clean "remote processor modem is now up" with no crash means the firmware loaded and the problem is above it.

## 3. Rule out ModemManager

ModemManager changes the modem's mode on its own, so take it out of the picture:

1. Stop ModemManager (`systemctl stop ModemManager`).
2. Restart the modem through `remoteproc` as above, and wait 10–15 s.
3. Read the mode **before anything touches it**, then try `online` by hand with `qmicli`.
4. Start ModemManager again afterwards.

If a freshly restarted modem is **already in `shutting-down`** with ModemManager stopped, and `online` is still refused, ModemManager is ruled out: the modem is giving up during its own startup, and something it needs from the Linux side in its first seconds is missing.

## 4. Compare with an OS where it works

If the modem works under another OS (Android, or Ubuntu Touch over Android's kernel and services), you have a known-good baseline: same SIM, same tower, same bands. That comparison rules out the SIM, the carrier account and the hardware, and tells you the fault is in the Linux userspace or kernel you are debugging.

## 5. Read the helper-service logs

The kernel loads the modem's firmware (`remoteproc`, from `/lib/firmware`). While it starts, the modem then relies on two small Linux services:

- **`rmtfs`** gives it its stored settings (EFS) from the `modemst1`, `modemst2` and `fsg` partitions.
- **`tqftpserv`** serves it files over **QRTR**: extra firmware such as `wlanmdsp.mbn` under `/readonly/…`, and a scratch folder, `/readwrite/`, where the modem saves temporary files. On Android that folder is persistent storage on the `persist` partition.

```sh
journalctl -b -u tqftpserv -u rmtfs --no-pager
ls -la /tmp/tqftpserv /var/lib/tqftpserv   # where the /readwrite files land, depending on the version
```

The tell is in `tqftpserv`'s log and that folder. The modem writes `lctoem.tmp` and `mcfg.tmp` (its configuration system, MCFG), then checks their size with **`RRQ … rsize=0 seek=0`**, about 30 times per boot. If the files in `/readwrite` **stay 0 bytes**, the modem cannot save and verify its configuration files and gives up, refusing `online`.

The cause is **`tqftpserv` 1.1.1**, the version packaged in postmarketOS v26.06: it treats "option requested with value 0" as "option not requested", so the modem's size checks on empty files never get the reply they expect. Upstream fixed it in pull request #50 (September 2026: "a stat-style query against an empty file and an explicit zero seek were silently dropped from the OACK").

## 6. The fix

Use the **upstream `tqftpserv`**, version 1.3 or newer. A known-good build is `v1.3-4-geb4b9a0` from `github.com/linux-msm/tqftpserv`, built on the phone itself with **meson** from a checkout in `/tmp/tq`, and installed as `/usr/local/bin/tqftpserv`.

Override the packaged service with a systemd drop-in at `/etc/systemd/system/tqftpserv.service.d/zz-upstream.conf`:

```ini
[Service]
ExecStart=
ExecStart=/usr/local/bin/tqftpserv
StateDirectory=tqftpserv
```

`StateDirectory=tqftpserv` makes its `/readwrite` persist at `/var/lib/tqftpserv` across reboots. Then:

```sh
sudo systemctl daemon-reload
sudo systemctl restart tqftpserv
```

With the fix, the modem registers **by itself shortly after boot**, data reconnects automatically, and it keeps coming up after a reboot without intervention. SMS works both ways (`mmcli --messaging-create-sms` to send, text quoted as `text='…'`; Chatty ingests the reply), and calls connect with `mmcli --voice-create-call`, which starts the call by itself. Call audio probably also needs **`q6voiced`**, which routes call audio; it is in the v26.06 repository but not installed by default.

The packaged 1.1.1 stays installed underneath. **If postmarketOS/Nura ever ships `tqftpserv` ≥ 1.3, remove the override** so the packaged one takes over.

**To undo:** delete the drop-in, then `systemctl daemon-reload` and `systemctl restart tqftpserv`.

## Noise you can ignore

- NetworkManager logs an MTU warning on `qrtr0` and a failure from `50-dns-filter.sh`; neither affects connectivity.
- In `tqftpserv`'s log, the modem tries several paths for `wlanmdsp.mbn` before the one that works, and asks for `ota_firewall/ruleset`, which doesn't exist.
- The userspace `pd-mapper` service may not be installed: the kernel's built-in `qcom_pd_mapper` answers the modem's service lookups on current kernels.
- Swapping in the phone variant's own modem firmware is not needed for this fix (on the Redmi Note 9 Pro Max it made no difference; see [`phones/xiaomi-excalibur`](../../../../phones/xiaomi-excalibur/)).

## Last verified

Modem working (LTE, auto-registration after reboot), SMS both ways, calls connecting: 2026-11-21. Call audio not verified. Checked 2026-11-21.
