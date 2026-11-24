# Reverse engineering

Reverse engineering answers four questions in order: what is this file, what does it talk to, where does untrusted input enter, and what happens to that input. Tools come after the questions. A disassembler opened without a hypothesis becomes a 30,000-function screensaver.

This guide covers static and dynamic work on ELF, PE, Mach-O, APK, IPA, firmware, and managed runtimes. Use it on artifacts you own or are authorized to inspect.

## First, scope the artifact

| Question | Why it matters |
|---|---|
| Where did the file come from? | Vendor, build pipeline, sample source, app store |
| What platform and architecture? | Chooses the loader, debugger, and calling convention |
| Do I have symbols or source? | Changes the workflow completely |
| Is it packed, signed, or obfuscated? | Determines whether static work is worth starting |
| What is the target behavior? | Parsing, network, auth, crypto, update, plugin load |
| What is the proof I need? | A crash, a key, a protocol, a hidden endpoint |

Write the hypothesis before opening a tool. "The update client trusts a manifest field that reaches a filesystem write" is a hypothesis. "Understand the binary" is not.

## Tool ladder

Start with the cheapest tool that can falsify the hypothesis.

| Rung | Tool | Use | Cost |
|---|---|---|---|
| 0 | `file`, `strings`, `xxd`, `readelf`, `objdump` | Format, sections, imports, obvious strings | Free |
| 1 | `rizin` / `radare2` | Fast triage, xrefs, function list, scripting | Free |
| 2 | Ghidra | Decompiler, cross-platform, headless batch | Free |
| 2b | Ghidrust | Rust-native CLI, MCP and GUI | Free |
| 3 | Binary Ninja | Faster UI, BNIL, strong Python API, headless | Commercial |
| 4 | IDA Pro + Hex-Rays | Best decompiler, largest plugin ecosystem | Commercial |
| 5 | Debugger and instrumentation | Confirm runtime behavior | Free to commercial |

The ladder is about cost, not quality. Ghidra is often enough. Binary Ninja and IDA earn their price on large binaries, managed code, and plugin-heavy workflows.

| Task | Better tool |
|---|---|
| Unknown file, quick look | `file`, `strings`, `rizin` |
| Large C/C++ binary, decompile | Ghidra or IDA |
| Scripted batch analysis | Ghidra headless, Binary Ninja API, `rizin -j` |
| Windows user-mode debugging | x64dbg |
| Windows kernel and crash dumps | WinDbg + TTD |
| Linux user-mode debugging | GDB + pwndbg or gef, rr |
| Android DEX | jadx, apktool |
| .NET | dnSpyEx, ILSpy |
| Firmware | unblob, binwalk, EMBA, QEMU |
| Symbolic exploration | angr, Triton, Miasm |
| Capability summary | capa, FLOSS |

Ghidra and GhidRust are different. GhidRust is a paused Ghidra plugin for Rust binaries. Ghidrust is a separate Rust-native tool. Read `ghidra-ecosystem.md` before installing either.

## Ghidra

Ghidra is free, cross-platform, and has a real decompiler. It needs JDK 25 and 4 GB of RAM minimum. Install it by extracting a release archive, never by copying over an old install.

```bash
# after installing JDK 25 and downloading the release zip
unzip ghidra_12.2_PUBLIC_2026.zip -d ~/tools
~/tools/ghidra_12.2_PUBLIC/support/ghidraRun
```

Read `docs/GettingStarted.md` in the install directory and `support/analyzeHeadlessREADME.html` before batch work. Ghidra ships with known security advisories; check them before opening untrusted files.

### GUI reading order

1. Imports and exports. Note the libraries and the entry point.
2. Defined strings. Sort by reference count.
3. Symbol references from strings into functions.
4. Decompiler on each caller. Rename and retype as you read.
5. Function graph for loops, switch tables, and indirect calls.
6. Data type manager for structs. Build the struct before reading the parser.
7. Xrefs on every API that touches files, sockets, memory, or crypto.

Name functions as you learn them. Names are durable knowledge. A note that says "function at 0x4012a0 is interesting" is not.

Do not accept the first decompilation as truth. Ghidra guesses types and can hide a bug behind a clean-looking cast. Check the assembly for every branch that matters.

### Headless mode

```bash
~/tools/ghidra/support/analyzeHeadless /tmp/ghidra-proj MyProject \
  -import ./sample.bin -overwrite \
  -postScript dump.py -scriptPath ./scripts
```

The analyzer writes a project under `/tmp/ghidra-proj`. A postscript can export functions, imports, strings, xrefs, and decompiled text to JSON.

Minimal postscript (`scripts/ghidra_dump.py`):

```python
# Ghidra Python (Jython) postscript
import json
from ghidra.program.model.symbol import SymbolType

out = {"program": currentProgram.getName(), "functions": [], "imports": [], "strings": []}
for f in currentProgram.getFunctionManager().getFunctions(True):
    out["functions"].append({"name": f.getName(), "entry": str(f.getEntryPoint()),
                             "size": f.getBody().getNumAddresses()})
for s in currentProgram.getSymbolTable().getAllSymbols(True):
    if s.getSymbolType() == SymbolType.FUNCTION:
        for ref in getReferencesTo(s.getAddress()):
            out["imports"].append({"name": s.getName(), "from": str(ref.getFromAddress())})
for d in currentProgram.getListing().getDefinedData(True):
    if d.getValue() and isinstance(d.getValue(), str):
        out["strings"].append({"address": str(d.getAddress()), "value": d.getValue()[:200]})
with open("/tmp/ghidra-dump.json", "w") as fh:
    json.dump(out, fh, indent=2)
print("wrote /tmp/ghidra-dump.json")
```

PyGhidra runs CPython inside Ghidra. It is useful when the analysis script needs pip packages:

```bash
~/tools/ghidra/support/pyghidraRun
python -m pip install --no-index -f ~/tools/ghidra/Ghidra/Features/PyGhidra/pypkg/dist pyghidra
```

### Ghidra pitfalls

- The decompiler is a view, not the binary. Verify memory accesses in assembly.
- Auto-analysis can miss indirect calls and C++ virtual dispatch. Add xrefs by hand.
- Large binaries need more RAM than the default launcher gives.
- A project upgrade is one-way. Back up `.rep` and `.gpr` first.
- Extensions are code. Install only ones you trust.
- Ghidra's BSim finds similar functions, not identical bugs. Triage its matches.

## Binary Ninja

Binary Ninja is a commercial reverse-engineering platform with a fast UI, multiple intermediate languages, and a strong Python API. Headless use needs a Commercial, Ultimate, or headless license. The free and non-commercial licenses are for interactive work.

```bash
# install the API into a dedicated Python
python3 ~/binaryninja/scripts/install_api.py
```

Minimal headless script:

```python
import binaryninja
with binaryninja.load("/bin/ls") as bv:
    print(bv.file.filename, len(list(bv.functions)))
```

Use `update_analysis=False` for a fast header-only pass. Use `analysis.initialAnalysisHold` to analyze one function. Use `binaryninja.disable_default_log()` in batch jobs. For multiprocessing, use the `spawn` or `forkserver` start method and lower the worker thread count. `fork` hangs because BN is multithreaded.

The intermediate languages matter. LLIL is close to assembly, MLIL removes flags, HLIL is close to C. Read at the lowest level that still answers the question. A type confusion is often obvious in MLIL and invisible in HLIL.

Binary Ninja workflows:

- **Cross references** on every import and string.
- **Type archives** for Windows and libc structures.
- **Guided analysis** to seed known functions.
- **Debugger** with TTD on Windows and GDB RSP time travel on Linux.
- **BinExport** to move results into BinDiff.
- **Firmware Ninja** for embedded images.
- **MCP server** to let an agent query the database.

Pitfalls:

- Headless without a suitable license fails late.
- Multiprocessing without `spawn` crashes.
- HLIL hides pointer arithmetic. Drop to MLIL.
- Plugins can change analysis. Disable user plugins in reproducible jobs with `BN_DISABLE_USER_PLUGINS=True`.

## IDA Pro

IDA is the industry default for a reason: Hex-Rays output is the best decompiler and the plugin ecosystem is deep. It is also expensive. IDA Free is useful for small binaries but lacks the decompiler.

```bash
idat64 -A -S"dump.py" -L/tmp/ida.log sample.exe
```

Workflow notes:

- Load with the correct processor and base. A wrong base poisons every xref.
- Use `Shift+F12` for strings only after the import table.
- Build structs in Local Types before reading a parser.
- Use `lumina` or FLIRT signatures when available.
- Check the call graph for indirect calls before trusting a "no xrefs" result.
- `BinDiff` against the previous version is often faster than reading the patched function.

Pitfalls:

- The decompiler can hide integer truncation behind a cast.
- Aggressive optimization inlines the function you need. Build with `-O0` if source is available.
- IDA databases are versioned. Keep the `.idb` or `.i64` with the sample.

## radare2, rizin, and Cutter

`radare2` and the `rizin` fork are command-first frameworks. Cutter is a Qt front end for rizin. They are fast for triage and easy to script.

```bash
rizin -A ./sample
[0x00000000]> iI          # file info
[0x00000000]> iS          # sections
[0x00000000]> iE          # exports
[0x00000000]> ii          # imports
[0x00000000]> izz         # strings
[0x00000000]> afl         # functions
[0x00000000]> axt @ sym.imp.strcpy
[0x00000000]> pdf @ main
```

JSON output:

```bash
rizin -q -j -c 'iI;iS;iE;ii;iz' ./sample > triage.json
```

Common plugins: `r2ghidra` for decompilation, `r2dec`, `r2yara`, `r2frida`. Install with `r2pm -ci r2ghidra`.

Pitfalls:

- `aaa` is expensive on large binaries. Start with `aa`.
- Command syntax changed between r2 and rizin. Check the version.
- Visual mode is fast, but the command log is the evidence.

## Debuggers

### Linux

```bash
gdb -q ./sample
(gdb) set pagination off
(gdb) run $(python3 -c 'print("A"*300)')
(gdb) info registers
(gdb) x/40gx $rsp
(gdb) bt
```

Use `pwndbg` or `gef` for heap inspection, telescope, and mitigation checks:

```bash
gdb -q -ex 'source ~/pwndbg/gdbinit.py' ./sample
```

`rr` records and replays a run:

```bash
rr record ./sample input
rr replay
```

### Windows

x64dbg is the user-mode debugger for breakpoints, memory maps, and call stacks. WinDbg with TTD records a trace you can rewind. Both need a Windows VM.

```
0:000> g
0:000> k
0:000> r
0:000> !analyze -v
0:000> .ttime
```

### Apple

```bash
lldb ./sample
(lldb) run
(lldb) bt
(lldb) register read
```

On modern iOS, use a corellium-style lab or a jailbroken device.

## Dynamic instrumentation

Frida injects JavaScript into a running process:

```bash
frida -U -f com.example.app -l hook.js --no-pause
frida-trace -U -f com.example.app -i "open*" -i "SSL_*"
```

Use it to log arguments, bypass pinning in a lab, and dump decrypted buffers. It is not a substitute for static understanding. A hook without a symbol or an address is a guess.

Other instrumentation:

- **DynamoRIO** for instruction-level tracing and coverage.
- **Intel PIN** for dynamic analysis on x86.
- **API Monitor** and **Procmon** for Windows API and filesystem behavior.
- **strace** and **ltrace** for syscall and library call traces on Linux.
- **Wireshark** and **mitmproxy** for network behavior.

## Format triage

### ELF

```bash
file ./sample
readelf -hW ./sample
readelf -lW ./sample | grep -E "GNU_STACK|GNU_RELRO|INTERP"
readelf -dW ./sample | grep -E "RPATH|RUNPATH|BIND_NOW|NEEDED"
objdump -d ./sample | head -80
strings -a ./sample | head -200
```

### PE

```bash
file ./sample.exe
objdump -p ./sample.exe | head -100
strings -a -n 6 ./sample.exe | grep -Ei "http|cmd|powershell|token"
```

Check the load config, DLL characteristics, signature, section entropy, and overlay. A high-entropy section plus a small import table suggests packing.

### Mach-O

```bash
file ./sample
otool -hL ./sample
otool -l ./sample | grep -A4 LC_LOAD_DYLIB
codesign -dvvv ./sample
```

### Java, .NET, Go, Rust

- Java: `jadx`, `cfr`, `procyon`. Read the decompiled source, not smali, unless obfuscated.
- .NET: `dnSpyEx` or `ILSpy`. Check for ConfuserEx and .NET Reactor.
- Go: use `redress`, `go-re.tk`, or `GoReSym` to recover function names and build info.
- Rust: symbols are mangled. `rustfilt` demangles. Panic strings reveal module paths.

## Static workflow

1. **Headers and sections.** Note architecture, entry point, writable and executable regions, overlay.
2. **Imports.** Group by behavior: file, network, process, crypto, registry, UI.
3. **Strings.** Look for URLs, error messages, format strings, paths, and user agents.
4. **Entry point.** Follow to the real main, not the runtime stub.
5. **Input sources.** argv, stdin, socket, file parser, IPC, environment, registry.
6. **Sinks.** `strcpy`, `memcpy`, `sprintf`, `system`, `CreateProcess`, `write`, SQL, template.
7. **Data flow.** Trace from source to sink. Note every bounds check and allocation size.
8. **Types.** Build structs at the parser. A wrong struct hides the bug.
9. **Concurrency.** Locks, refcounts, queues, callbacks.
10. **Crypto.** Identify the algorithm and the key source. Check for hardcoded IVs and ECB.

Confidence labels: confirmed, inferred, speculative. Put the label in every note.

## Anti-analysis

| Trick | Signal | Counter |
|---|---|---|
| Packing (UPX, Themida, VMProtect) | High entropy, few imports, writable executable section | `upx -d`, dump at OEP, capa packer rules |
| String encryption | Few readable strings, decrypt loop at startup | Break after decrypt, dump memory, FLOSS |
| API hashing | No named imports, manual PEB walk | Resolve the hash loop, annotate the table |
| Anti-debug | `IsDebuggerPresent`, `ptrace`, timing checks | Patch the check, hide the debugger, use ScyllaHide |
| VM detection | CPUID, registry, MAC prefixes | Rename devices, patch the branch |
| Control-flow flattening | Dispatcher loop with a state variable | Trace the state, use a devirtualizer |
| TLS pinning | Connection fails with a proxy | Frida hook on the verifier in a lab |
| Junk code | Dead branches, opaque predicates | Use the decompiler's simplification, ignore unreachable blocks |

## Patch diffing

Compare the patched and unpatched versions to find the fix. This is the fastest route to an n-day.

```bash
# IDA + BinDiff
bindiff --primary old.idb --secondary new.idb --output diff.BinDiff

# Diaphora inside IDA
# File -> Diaphora -> Diff

# Ghidriff (Ghidra headless)
ghidriff old.bin new.bin -o diff.md
```

Read the changed functions, not the similarity score. A one-line bounds check is the finding. A refactor is noise.

## Symbolic and concolic execution

Use these when a branch is hard to reach and the input format is small.

```python
import angr
p = angr.Project("./sample", auto_load_libs=False)
st = p.factory.entry_state()
sm = p.factory.simulation_manager(st)
sm.explore(find=0x401234, avoid=0x401300)
print(sm.found[0].posix.dumps(0))
```

Triton and Miasm offer finer control over symbolic expressions. KLEE and S2E work on LLVM bitcode and whole systems. All of them struggle with large binaries, crypto, and environment-heavy code.

## Evidence and reporting

For each finding, keep:

- the file hash (SHA-256) and build version;
- the tool and version;
- the address, function, and decompiled snippet;
- the input that reaches the path;
- the runtime proof if you have one;
- the confidence label;
- the patch or mitigation.

Report the primitive, not the crash. "Controlled 8-byte write at 0x..." is a primitive. "It crashes" is an observation.

## Learning path

1. `file`, `strings`, `readelf`, `objdump` on small CTF binaries.
2. GDB and pwndbg until registers and stack frames are routine.
3. Ghidra on the same binaries. Rebuild the logic in C.
4. One parser bug per week: image, archive, PDF, protocol.
5. Fuzzing with AFL++ and a harness.
6. Crash triage and exploitability with CASR and a debugger.
7. One mitigation bypass at a time: NX, ASLR, RELRO, CFG.
8. Patch diffing on an open-source project.
9. Firmware and mobile once the desktop workflow is solid.

## Sources

- Ghidra `GettingStarted.md` and `analyzeHeadlessREADME.html`
- Binary Ninja batch and API documentation
- `0xSteph/pentest-ai-agents` reverse-engineer agent
- `mukul975/anthropic-cybersecurity-skills` Ghidra, jadx, Frida skills
- `SnailSploit/Claude-Red` exploit development, crash analysis, shellcode
- `The-Art-of-Hacking/h4cker` reverse-engineering tool list
- `skerkour/black-hat-rust` exploit and tooling examples
