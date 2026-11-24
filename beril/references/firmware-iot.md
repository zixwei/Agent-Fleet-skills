# Firmware and IoT

Firmware is a filesystem, a bootloader, and a set of daemons in one blob. The work starts with extraction, then moves to services, update logic, and hardware interfaces. An IoT device without a shell is still a set of parsers with network input.

Use wired Ethernet and an isolated VLAN. Assume every daemon is hostile until you know it.

## Intake

Record:

- vendor, model, hardware revision;
- firmware version and download URL;
- hash;
- update method: web upload, USB, auto-update, mobile app;
- radio set: Wi-Fi, BLE, Zigbee, LoRa, cellular;
- exposed interfaces: Ethernet, USB, UART, JTAG, SPI, SD.

Photograph the board before touching it. Label connectors.

## Extraction

```bash
unblob firmware.bin -e ./out
binwalk -eM firmware.bin
binwalk --dd='.*' firmware.bin
```

`unblob` handles 78+ formats and is the better default. It needs external extractors. Docker avoids the dependency setup:

```bash
docker run --rm -v "$PWD:/data/input" -v "$PWD/out:/data/output" \
  ghcr.io/onekey-sec/unblob:latest /data/input/firmware.bin
```

`binwalk -eM` recursively extracts known signatures. It misses formats and can create a directory tree that is hard to clean.

Manual carving:

```bash
file firmware.bin
xxd firmware.bin | head
strings -a -n 8 firmware.bin | grep -Ei "squashfs|jffs2|ubi|uImage|rootfs"
```

EMBA automates firmware triage:

```bash
sudo ./emba -f firmware.bin -l ./logs
```

It runs extraction, binary hardening, secret checks, and CVE matching. Read the report, do not trust the score.

## Filesystem

Mount read-only:

```bash
mkdir rootfs
sudo mount -o ro,loop rootfs.img rootfs
```

Or extract with `unsquashfs`, `jefferson` for JFFS2, `ubireader_extract_images` and `ubireader_extract_files` for UBI.

Immediately look for:

```bash
cat etc/passwd etc/shadow
find . -name "*.conf" -o -name "*.cfg" -o -name "*.ini" | head -50
grep -RIn "admin\|password\|secret\|token" etc/ usr/etc/ 2>/dev/null | head -80
ls -la etc/init.d etc/rc.d
find . -perm -4000 -type f
```

Check the web root, CGI, and any `cgi-bin`. A shell script behind a web form is a common command injection site.

## Services

```bash
find . -type f -perm -111
file $(find . -type f -perm -111) 2>/dev/null | grep ELF
```

Map listening ports from the config, not only from a live scan:

```bash
grep -RIn "listen\|bind\|port" etc/ 2>/dev/null | head -80
```

A service with no authentication on the LAN is still a finding if the LAN is the trust boundary.

## Update path

Update logic is the highest-value target.

- Is the image signed? Which key and where is it stored?
- Does the updater verify the signature before extraction?
- Is the version checked, or can you downgrade?
- Is the archive extracted as root?
- Is the manifest parsed before or after the signature check?
- Can a filename traverse out of the update directory?

Test with a re-packed image and a modified manifest. Keep a recovery method.

## Hardware

### UART

Find the pads: TX, RX, GND, often 3.3 V. Use a multimeter and a logic analyzer. A UART usually has one pad that idles high (TX).

```bash
picocom -b 115200 /dev/ttyUSB0
```

Common baud rates: 115200, 57600, 38400, 9600. If the console asks for a login, try the extracted `/etc/shadow` or a known vendor default.

### JTAG and SWD

OpenOCD with the right target config:

```bash
openocd -f interface/ftdi/ftdi.cfg -f target/stm32f1x.cfg
telnet localhost 4444
```

Dump flash:

```text
dump_image flash.bin 0x08000000 0x100000
```

### SPI flash

Use a SOIC-8 clip and a programmer such as CH341A or Flashrom.

```bash
flashrom -p ch341a_spi -r dump.bin
```

Read the chip twice and compare. A bad read produces a corrupt image.

## Emulation

FirmAE automates full-system emulation for router firmware:

```bash
sudo ./run.sh -r linksys fw.bin
```

QEMU user mode for a single binary:

```bash
qemu-mipsel -L ./rootfs ./rootfs/usr/bin/httpd
```

Full-system QEMU needs a kernel and an initrd that matches the target. A wrong machine type produces a silent hang.

NVRAM is often the blocker. Stub the `nvram` library or preload a shim that returns expected keys.

## Binary analysis

All firmware binaries are artifacts. Run `./beril scan --target ./rootfs/usr/bin/httpd --deep`, then open the interesting ones in Ghidra.

Look for:

- `system`, `popen`, `execve` with config or request data;
- `strcpy`, `sprintf`, `sscanf` on request fields;
- hardcoded credentials and private keys;
- custom crypto and rolling-code schemes;
- debug endpoints and hidden CGI;
- command injection through `hostname`, `ntp`, `ping`, `traceroute`;
- stack overflows in the management daemon.

## Secrets

Grep the whole rootfs for:

```bash
grep -RInE "AKIA[0-9A-Z]{16}|-----BEGIN|password|passwd|secret|api[_-]?key" . 2>/dev/null | head -100
```

Check `/etc/shadow` hashes, SSH host keys, TLS private keys, cloud tokens, and update signing keys. A shared vendor key across models is a supply-chain finding.

## Radio

- BLE: `btmon`, `gatttool`, `bettercap`.
- Zigbee: `zigbee2mqtt`, a sniffer, and the IEEE address.
- Wi-Fi: `aircrack-ng`, `hcxdumptool`, `hostapd-wpe`.
- Sub-GHz: `rtl_433`, a HackRF, or a Flipper Zero.

Capture the pairing and the command frames before replaying. A replay that works once is not a protocol break.

## Reporting

Include:

- model, hardware revision, firmware version and hash;
- extraction path and rootfs access;
- service, port, and request;
- shell or file proof;
- whether authentication is required;
- recovery steps you used;
- the fix: signed updates, non-root daemons, input bounds, unique keys.

State whether the device is at the perimeter or behind NAT. A root shell on the LAN is different from one on the WAN.

## Pitfalls

- Bricking the device with a bad flash. Dump before writing.
- Testing on a device that auto-updates. Pin the firmware.
- Assuming a mounted filesystem is complete. Some files are generated at boot.
- Trusting `strings` alone. A config path may be assembled at runtime.
- Forgetting the mobile app. It often holds the cloud API and the BLE protocol.
- Emulating with the wrong endianness. Check the ELF header.

## Sources

- unblob documentation
- EMBA documentation
- FirmAE
- OWASP IoT Top 10
- `mukul975/anthropic-cybersecurity-skills` firmware skills
- `GhostTroops/scan4all` and `lynis` for host checks
