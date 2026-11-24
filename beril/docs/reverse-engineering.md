# Reverse engineering quick start

Deep work starts with triage, then a hypothesis, then one tool at a time. Full method: `references/reverse-engineering.md`.

## Install the optional stack

```bash
./install.sh --re
```

That adds capa, FLOSS, YARA, capstone, keystone, ROPgadget, pwntools, Frida, objection, and unblob to the Beril environment.

External tools:

```bash
# Arch
sudo pacman -S rizin radare2 binwalk qemu-full

# Debian/Ubuntu
sudo apt install rizin radare2 binwalk qemu-system-arm
```

Ghidra: download a release from https://github.com/NationalSecurityAgency/ghidra/releases, install JDK 25, extract, and set `GHIDRA_HOME`.

Binary Ninja: install from https://binary.ninja/, then run its `scripts/install_api.py`. Headless needs a Commercial, Ultimate, or headless license.

## Triage

```bash
./beril scan --target ./sample --deep
```

The base pass is static. If capa, FLOSS, or rizin is installed, the deep pass adds capabilities, decoded strings, and risky imports.

## GhidRust

`GhidRust` is a paused Ghidra extension for Rust binaries. It offers Rust detection and a libstd FunctionID database. Ghidra upstream Rust support is now better. Details: `references/ghidra-ecosystem.md` and `references/rust-reverse.md`.

## Ghidra headless

```bash
export GHIDRA_HOME=~/tools/ghidra
./scripts/ghidra_headless.sh ./sample scans/sample.ghidra.json
```

The wrapper imports the file, runs `ghidra_dump.py`, and deletes the temporary project. Open the JSON for functions, imports, strings, and suspicious calls. For real reading, open the same file in the Ghidra GUI.

## Rizin

```bash
./scripts/rizin_triage.sh ./sample scans/sample.rizin.json
rizin -A ./sample
[0x0]> axt @ sym.imp.strcpy
[0x0]> pdf @ main
```

## Patch diff

```bash
./scripts/patch_diff.sh ghidriff old.bin new.bin
./scripts/patch_diff.sh bindiff old.i64 new.i64
./scripts/patch_diff.sh git ./repo v1.2.3 v1.2.4
```

## Fuzzing

```bash
cp scripts/afl_harness.c .
# replace parse() with the target function
afl-clang-fast -g -O1 afl_harness.c parser.c -o fuzz_target
mkdir -p in out && cp seeds/* in/
afl-fuzz -i in -o out -m none -t 1000+ -- ./fuzz_target @@
```

## Mobile

```bash
apktool d app.apk -o app-src
jadx-gui app.apk
frida -U -f com.example.app -l scripts/frida_hook.js --no-pause
```

## Firmware

```bash
unblob firmware.bin -e ./out
sudo mount -o ro,loop ./out/rootfs.img ./rootfs
```

## Triage order

1. Hash and file type.
2. Mitigations and imports.
3. Strings and endpoints.
4. Entry point and input sources.
5. Sinks and data flow.
6. Debugger confirmation.
7. Primitive, impact, report.

A crash is a lead. A controlled register is a primitive. A working chain is an exploit.
