# Rust reverse engineering

Rust binaries carry a lot of structure: mangled symbols, panic strings, crate paths, trait vtables, and monomorphized copies. The structure helps until the binary is stripped. Then the same features become noise.

Use this on binaries you own or are authorized to inspect.

## First triage

```bash
file sample
strings -a sample | head -100
readelf -p .comment sample 2>/dev/null
```

Signals of Rust:

- strings with `rustc`, `cargo`, `.cargo/registry`, `src/main.rs`, `panicked at`
- mangled names with `_ZN`, `17h` hashes, `$LT$`, `..`
- `core::panicking`, `alloc::`, `std::` symbols
- `.rustc` section, DWARF with Rust producer strings
- many small functions and duplicated code from monomorphization

Ghidra upstream has Rust-specific support. Check its `Edit -> Tool Options -> Rust` settings before installing an old plugin.

## Symbols

```bash
nm -C sample | rustfilt | head
objdump -t sample | rustfilt | grep -E "::"
```

`rustfilt` demangles. `rust-demangle` is a lighter CLI. If symbols are stripped, recover them from panic metadata and function hashes, not from guesses.

## Crate and version recovery

Panic strings often contain absolute build paths:

```text
/home/user/.cargo/registry/src/index.crates.io-.../serde-1.0.203/src/de.rs
```

The path gives the crate and version. Extract:

```bash
strings -a sample | grep -oE '[a-z0-9_-]+-[0-9]+\.[0-9]+\.[0-9]+' | sort -u
```

A version lets you diff the crate against the upstream source.

## Data layout

Rust layouts differ from C.

| Type | Layout |
|---|---|
| `&[T]` / `Vec<T>` | pointer + length (+ capacity for Vec) |
| `&str` | pointer + length, not NUL terminated |
| `Option<&T>` | pointer, null niche |
| `Result<T, E>` | tag + union payload |
| `Box<T>` | pointer |
| trait object | data pointer + vtable pointer |
| closure | captured fields, call through `Fn` trait |
| `String` | pointer + length + capacity |
| enum | discriminant, then the largest variant |

In a decompiler, a two-field struct passed together is often a slice. A function pointer next to a data pointer is often a trait object. Name the fields before reading the method.

## Monomorphization

Generic functions are copied per type. The same logic appears with different field widths. Pick one copy, recover the generic body, then apply it to the others. A `HashMap<K,V>` has separate code for every `K,V`.

The size of the binary is not the size of the source. Do not count functions as features.

## Async and closures

Async functions become state machines with a discriminant and a poll method. A state enum plus a `Future::poll` call is the signature. Map the states to `await` points.

Closures become structs with captured fields and a generated `call` method. The field order matches the capture order.

## Trait objects and vtables

A vtable is a static array of function pointers, usually `drop`, `size`, `align`, then the trait methods. Cross-reference the vtable to find implementors. Ghidra's RTTI analyzer and `Ghidra-Cpp-Class-Analyzer` help only when type names survive.

## Panics and unwrap

`unwrap`, `expect`, and indexing generate panic paths with file and line strings. They mark the exact source location of an assumption. A reachable panic is a denial-of-service lead. A panic after a partial write is a state bug.

## Hash and build IDs

The `17h...` suffix is a crate disambiguator, not a build ID. The `.note.gnu.build-id` is the build ID. Record both when available.

## Allocator behavior

Rust uses the system allocator by default, jemalloc on some targets, and a custom allocator in some projects. Identify the allocator before reasoning about heap bugs. `malloc`/`free` breakpoints may land in a shim.

## Ghidra settings

- Enable the Rust analyzer only on a copy of the project.
- Run `Function ID` with the Rust libstd database when the version matches.
- Use `Demangler` on symbol imports.
- Treat the C-to-Rust view as a note, not output.

## GhidRust and Ghidrust

`DMaroo/GhidRust` is a paused Ghidra extension with Rust detection and a libstd FunctionID database. `oofz/Ghidrust` is a separate Rust-native CLI, MCP and GUI platform. Details and install steps are in `ghidra-ecosystem.md`.

## Tooling

| Tool | Use |
|---|---|
| `rustfilt` | Demangle symbols |
| `rust-demangle` | Lighter demangler |
| `goblin` | Rust crate for ELF/PE/Mach-O parsing |
| `object` | Rust object file parsing |
| `gimli` | DWARF parsing |
| `cargo-audit` | Dependency CVEs when source is available |
| `cargo-metadata` | Crate graph from source |
| `Ghidra` | Disassembly and decompilation |
| `rizin` | Fast triage and xrefs |
| `angr` | Symbolic work on small state machines |

## Pitfalls

- Mangled names are long. Demangle into a side file, not into the database, until you are sure.
- `&str` is not NUL terminated. A C-string view truncates.
- Vtables are data, not code. Mark them as pointers before reading xrefs.
- The allocator may be custom. Check before heap work.
- Rust safety does not remove integer, logic, or parser bugs.
- A panic is not automatically a vulnerability. Prove reachability and impact.

## Sources

- Ghidra Rust commits GP-2412, GP-4158, GP-4103
- `DMaroo/GhidRust` README and report
- `oofz/Ghidrust` README
- `rustfilt`, `goblin`, `object`, `gimli` documentation
- Rust reference on type layout and trait objects
