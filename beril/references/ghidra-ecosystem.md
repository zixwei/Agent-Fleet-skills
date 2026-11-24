# GhidRust and the Ghidra plugin ecosystem

Two projects share a similar name. They are different tools.

| Project | What it is | Status |
|---|---|---|
| `DMaroo/GhidRust` | A Ghidra extension for Rust binaries. Rust detection, a libstd FunctionID database, and an experimental C-to-Rust decompiler view. | Development paused, partially usable |
| `oofz/Ghidrust` | A separate Rust-native RE platform with a CLI, MCP server and GUI. Not a Ghidra plugin. | Active |

Do not confuse them in a report. GhidRust extends Ghidra. Ghidrust replaces the workflow around it.

## DMaroo/GhidRust

Repo: https://github.com/DMaroo/GhidRust  
License: MIT  
Language: Java  
Stars: 391 at collection time

GhidRust was a course project. It adds:

- `GhidRust -> Check if Rust binary`, a heuristic Rust detector.
- `RustStdAnalyzer`, which applies FunctionID signatures from a bundled `libstd.fidb` for x86-64 and Rust 1.58.1.
- `GhidRust -> Open decompiler`, a panel that parses Ghidra's C output and tries to emit Rust. It is flaky.

The README is explicit: Ghidra upstream Rust support has improved past the plugin. Use GhidRust for the FunctionID database on old stripped Rust samples, and for the Rust view when it helps. Do not expect the Rust output to compile.

### Build and install

Needs JDK 21 or newer, Gradle, and a matching Ghidra install. The build script calls `gradle -PGHIDRA_INSTALL_DIR=...`.

```bash
git clone https://github.com/DMaroo/GhidRust
cd GhidRust
./build.sh -g /path/to/ghidra          # build only
./build.sh -ig /path/to/ghidra         # build and install
```

Then in Ghidra:

1. `File -> Install Extensions...`
2. Select GhidRust.
3. Restart.
4. `File -> Configure -> Miscellaneous`, enable `RustDecPlugin`.

### When to use it

- A stripped Rust binary on an older Ghidra where upstream detection is weak.
- You need a known `libstd` function match to label a call site.
- You want a second view of a small function and can verify the Rust by hand.

### When to skip it

- Ghidra already identifies the binary as Rust and names std functions.
- The binary uses a Rust version far from 1.58.1.
- The Rust output would need a rewrite anyway.

### Pitfalls

- The FunctionID database is version-specific. A mismatch gives wrong or missing names.
- The C-to-Rust transpiler is pattern-based. It loses types, lifetimes, and macro structure.
- The plugin is unmaintained. Check it against your Ghidra version before a long session.
- Extensions are code. Read the source before installing into a trusted project.

## oofz/Ghidrust

Repo: https://github.com/oofz/Ghidrust  
License: Apache-2.0  
Language: Rust  
Stars: 34 at collection time

A separate Rust-native platform. It loads PE/ELF, runs analyzers, decompiles, exposes an MCP server, and has a GUI. The README claims a Ghidra-shaped workflow without the JVM, plus IL2CPP and a network plane. Treat the performance claims as targets, not guarantees.

```bash
git clone https://github.com/oofz/Ghidrust
cd Ghidrust
cargo build --workspace --release
./target/release/ghidrust help
```

Use it when an agent needs a headless decompiler with a JSON or MCP surface and Ghidra is too heavy. Verify output on a known binary before trusting a finding.

## Plugin safety

Ghidra plugins run with your user rights and can read the project and the filesystem.

1. Prefer a release over a random build.
2. Check the last commit and the Ghidra version in `extension.properties`.
3. Read `build.gradle` and any install script.
4. Install into a test Ghidra, not the only one.
5. Keep plugins out of a malware analysis VM unless you need them.

## Install patterns

Most plugins use one of these. A missing release means building from source.

```text
# release zip
File -> Install Extensions -> green plus -> select zip -> restart

# Gradle
GHIDRA_INSTALL_DIR=/path/to/ghidra gradle

# script only
copy .py into <Ghidra>/Ghidra/Features/Base/ghidra_scripts
Script Manager -> refresh
```

## High-signal plugins

| Plugin | Stars | Use |
|---|---:|---|
| `bethington/ghidra-mcp` | 6322 | 200+ MCP tools for agent-driven RE |
| `symgraph/GhidrAssistMCP` | 819 | Native MCP server extension |
| `symgraph/GhidrAssist` | 783 | LLM assistant inside Ghidra |
| `weirdmachine64/GhidraGPT` | 847 | LLM prompt/explain integration |
| `ghidraninja/ghidra_scripts` | 1186 | Broad script collection |
| `HackOvert/GhidraSnippets` | 895 | Program and decompiler API snippets |
| `mandiant/Ghidrathon` | 793 | Python 3 scripting in Ghidra |
| `justfoxing/ghidra_bridge` | 364 | Python bridge for external tooling |
| `google/binexport` | 1215 | BinExport for BinDiff |
| `clearbluejar/ghidriff` | 824 | Headless patch diffing |
| `binsync/binsync` | 764 | Collaborative reversing over git |
| `bootleg/ret-sync` | 2416 | Debugger and disassembler sync |
| `KeenSecurityLab/BinAbsInspector` | 1685 | Binary vulnerability scanner |
| `fkie-cad/cwe_checker` | 1363 | CWE pattern checks on binaries |
| `trailofbits/BTIGhidra` | 179 | Binary type inference |
| `astrelsky/Ghidra-Cpp-Class-Analyzer` | 651 | C++ RTTI and class recovery |
| `Nalen98/AngryGhidra` | 624 | angr inside Ghidra |
| `Nalen98/GhidraEmu` | 309 | Native pcode emulator |
| `d3v1l401/FindCrypt-Ghidra` | 554 | Crypto constant scan |
| `TorgoTorgo/ghidra-findcrypt` | 303 | Crypto constant scan |
| `nccgroup/Cartographer` | 390 | Code coverage exploration |
| `0ffffffffh/dragondance` | 300 | Coverage visualizer |
| `ubfx/BinDiffHelper` | 299 | BinDiff integration |
| `evilpan/jni_helper` | 674 | JNI signature recovery |
| `felberj/gotools` | 345 | Go binary assistance |
| `nccgroup/ghostrings` | 141 | Go string recovery |
| `Washi1337/ghidra-nativeaot` | 297 | .NET Native AOT analysis |
| `battelle/cantordust` | 422 | Binary diffing and visualization |
| `airbus-cert/Yagi` | 510 | IDA and Ghidra bridge |
| `Comsecuris/gdbghidra` | 326 | GDB visual bridge |
| `diommsantos/Gx64Sync` | 132 | Ghidra and x64dbg sync |
| `schlafwandler/ghidra_SavePatch` | 230 | Save patches back to the file |
| `cesena/ghidra2dwarf` | 230 | Export decompiled code as DWARF |
| `clearbluejar/ghidrecomp` | 161 | Headless decompiler CLI |
| `akiselev/ghidra-cli` | 268 | Headless automation CLI |
| `hyuunnn/Hyara` | 251 | YARA rule authoring |

Loaders for retro and embedded targets are in the plugin index: Switch, PSX, PS4, Vita, GameCube, N64, XBE, GBA, NES, SNES, Game Boy, Sega, Saturn, Amiga, WebAssembly, nanoMIPS, Hexagon, vmlinux, and device trees.

## A safe session

1. Snapshot the project.
2. Install into a test Ghidra.
3. Load a small known binary.
4. Check that the plugin menu appears and analysis still finishes.
5. Read the plugin's README for known Ghidra versions.
6. Keep the plugin list in the report if a finding depends on it.

A plugin result is a lead until the disassembly confirms it.
