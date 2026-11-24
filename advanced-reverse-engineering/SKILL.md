---
name: advanced-reverse-engineering
description: Perform authorized static and dynamic analysis of owned binaries, firmware and protocols to understand architecture, behavior, vulnerability root causes, patch differences and defensive indicators without producing weaponized payloads.
---

# Advanced reverse engineering and binary analysis

## Purpose

Support source-unavailable software, native applications, embedded firmware, protocol implementations and vulnerability root-cause investigations. Produce defensible technical findings from disassembly, decompilation, debugging, symbol and metadata analysis.

This skill is for understanding and verifying software behavior. It is not a workflow for weaponizing vulnerabilities or compromising third-party software.

## Prerequisites

- Written authorization identifies binaries/source, product/version, architecture, platform, test environment and permitted dynamic instrumentation.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Obtain original files and hashes from the owner; preserve immutable copies.
- Use offline analysis, disposable VMs and synthetic input. Dynamic testing must occur on an isolated local fixture or explicitly approved environment.
- Review license and intellectual-property restrictions before redistributing decompiled source or modified binaries.

## Tools

- **Ghidra:** disassembly, decompilation, data types and program analysis. [Project](https://github.com/NationalSecurityAgency/ghidra)
- **Rizin / Cutter:** reverse engineering and visual analysis. [Rizin](https://rizin.re/) · [Cutter](https://cutter.re/)
- **radare2:** command-line binary analysis. [Book](https://book.rada.re/)
- **IDA / Binary Ninja:** alternative commercial reverse-engineering environments; respect licensing. [IDA](https://hex-rays.com/ida-free) · [Binary Ninja](https://binary.ninja/)
- **GDB / LLDB / WinDbg:** controlled native debugging. [GDB](https://sourceware.org/gdb/) · [LLDB](https://lldb.llvm.org/) · [WinDbg](https://learn.microsoft.com/windows-hardware/drivers/debugger/)
- **readelf / objdump / llvm-objdump / checksec:** inspect executable format, symbols, relocations and hardening.
- **Frida:** dynamic instrumentation of an owned process. [Documentation](https://frida.re/docs/)

## Workflow

### 1. Preserve and classify the sample

Record source, hash, file type, architecture, ABI, compiler/toolchain clues, debug symbols, signing/certificate metadata, dependencies, package version and provenance. Run format identification on a copy and keep untrusted binaries isolated.

### 2. Establish program structure

Identify entry points, sections/segments, imports/exports, symbols, relocations, exception metadata, strings, resources, embedded scripts, runtime dependencies and signing state. Distinguish compiler-generated code from application logic where possible.

### 3. Build a reproducible static-analysis model

Create project databases with tool/version, language settings, memory map, symbol sources and analysis options. Trace trust boundaries, input parsing, file/network operations, authentication decisions, memory ownership and privilege boundaries. Document uncertain decompiler output and manually validate key instructions.

### 4. Perform bounded dynamic analysis

Use a local fixture, debugger and synthetic inputs. Record process arguments, loaded modules, file access, syscalls, IPC, network activity and relevant call stacks. Use snapshots and reset state between experiments. Do not attach a debugger to unrelated or production processes.

### 5. Analyze vulnerability root cause and patches

Compare vulnerable and fixed builds using reproducible binary/source diffs. Identify the changed function/data flow, preconditions, affected versions, mitigations and regression tests. Prefer a safe unit-level reproduction or sanitizer output over a live exploit demonstration.

### 6. Review native and platform protections

Assess ASLR, DEP/NX, stack canaries, RELRO, control-flow protections, code signing, sandboxing, entitlements/capabilities, secure boot and update verification. Interpret protections in context; a hardening flag alone does not prove the application is secure.

### 7. Validate conclusions

Cross-check decompiler findings with assembly, symbols, vendor advisories, source/patch references and reproducible test cases. Separate confirmed behavior from a hypothesis. Preserve minimal evidence such as function offset, call graph, hash and expected/observed behavior.

### 8. Report and retain provenance

Record sample hash, tool versions, analysis database, symbol source, offsets, test environment, reproduction limitations, licensing and evidence redactions. Keep original binaries and analysis databases in restricted storage.

## Output

Binary inventory, architecture and build metadata, call/data-flow notes, security boundary analysis, vulnerability root-cause explanation, patch comparison, hardening review and reproducible evidence.

## Safety constraints

- Analyze only owned or explicitly authorized software and firmware.
- Do not add shellcode, backdoors, persistence, stealth, credential theft, exploit chains or payload deployment.
- Do not modify vendor binaries for redistribution or execute untrusted samples outside a disposable sandbox.
- Do not access unrelated processes, host secrets or production user data.
- Stop if analysis triggers unexpected network behavior or escapes the isolated environment.

## References

- [Ghidra](https://github.com/NationalSecurityAgency/ghidra)
- [Rizin Book](https://book.rizin.re/)
- [Cutter](https://cutter.re/)
- [radare2 Book](https://book.rada.re/)
- [GDB documentation](https://sourceware.org/gdb/documentation/)
- [LLDB documentation](https://lldb.llvm.org/)
