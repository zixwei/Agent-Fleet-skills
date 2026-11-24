---
name: c64-asm
description: Write, build and test Commodore 64 6502 assembly with KickAssembler. Use when creating or changing C64 games, demos, intros or tools in assembly (.asm with KickAssembler syntax), setting up a new C64 project, fixing KickAssembler build errors, or when you need C64 hardware details (VIC-II, sprites, raster IRQs, SID, CIA joystick and keyboard, KERNAL calls, memory map). Pairs with the c64-ready skill, which runs and checks the built program.
---

# c64-asm: KickAssembler build → run → verify

Every change goes through the same loop:

1. **Edit** the `.asm` source. KickAssembler syntax only: see
   [reference/kickassembler.md](reference/kickassembler.md).
2. **Build** it (see [Building](#building) below). This writes `build/main.prg`,
   `build/main.sym` and `build/main.vs`. A failed build exits 1 and prints the error
   with its line and column.
3. **Run and check** it with the **c64-ready** skill:
   `npx -y -p c64-ready@3.3.1 c64-agent build/main.prg --symbols build/main.sym --frames 100 --screen --screenshot /tmp/shot.png`
4. Compare what happened with what you intended (the screen text, memory dumps, a
   screenshot, or a breakpoint being hit), then go back to step 1.
5. **Hand it over.** Tell the user what you checked, and how to play it in the
   c64-ready browser player: `npx c64-ready build/main.prg --open`. See
   "Handing the program to the user" in the c64-ready skill. Offer VICE only as an
   alternative.

Never report C64 code as done until it has been built and run. A program that
assembles can still crash, draw nothing, or read the wrong joystick port.

## Building

You need **Java 8+** (`java -version`) and **KickAss.jar**. Find the jar once per
session by checking these in order, and use the first that exists:

1. `$KICKASS_JAR`
2. `./tools/KickAss.jar`, `./tools/bin/KickAss.jar`, `./KickAss.jar`
3. `~/KickAssembler/KickAss.jar`
4. `/opt/KickAss.jar`, `/opt/KickAssembler/KickAss.jar`, `/usr/local/share/KickAssembler/KickAss.jar`
5. `/Applications/KickAssembler/KickAss.jar` (macOS)

If none exists, ask the user where it is, or tell them to download KickAssembler (free,
https://theweb.dk/KickAssembler/) and set `KICKASS_JAR`. Don't download it yourself.

Then build from the project root:

```bash
mkdir -p build
java -jar "$KICKASS_JAR" src/main.asm \
  -o "$PWD/build/main.prg" -odir "$PWD/build" -symbolfile -vicesymbols
```

- Always pass **absolute** paths to `-o` and `-odir`. KickAssembler resolves `-o`
  against the current directory but `-odir` against the source file, so relative paths
  scatter the output.
- `-symbolfile` writes `build/main.sym`, which c64-agent's `--symbols` reads.
  `-vicesymbols` writes `build/main.vs` for VICE.
- A failed build exits 1. The error is at the end of the output, as
  `Error: …` followed by `at line N, column M in file.asm`. The lines before it
  (`parsing`, `flex pass`, `Output pass`) are progress messages.
- Add extra flags at the end, for example `-showmem -define DEBUG`.

## Starting a new project

Write the code block in [templates/main-asm.md](templates/main-asm.md) to `src/main.asm`. It's a small tested
starting point: a BASIC stub, a raster IRQ that sets a once-per-frame flag, a main loop
locked to that flag, and a joystick-driven sprite. A suggested layout:

```
src/main.asm        entry point: BasicUpstart2, init, main loop
src/*.asm           modules, pulled in with #import "x.asm" (and #importonce at the top of each)
assets/             .bin / .sid / .png, loaded with .import binary / LoadSid / LoadPicture
build/              generated: .prg .sym .vs (add to .gitignore)
```

To check that the template works with joystick input:
```bash
# build as in "Building" above, then:
npx -y -p c64-ready@3.3.1 c64-agent build/main.prg --frames 60 --at 10:joy2=right --at 40:joy2=none --dump 'd000:2'
# d000: be 8c   → sprite X moved from 160 to 190 (30 frames of "right")
```

## Writing testable code

- **Labels everywhere that matters** (`player_x:`, `score:`, `game_over:`). With
  `--symbols`, you can then `--dump score:3` and `--break game_over` by name, and the
  report shows the PC as `mainLoop+2` instead of a bare address.
- **Prove behaviour with memory, not only screenshots.** To check that "pressing fire
  spawns a bullet", dump the bullet table after `--at 20:joy2=fire`, or break on
  `spawn_bullet`.
- **Self-checking tests:** for logic such as maths, collision or score BCD, write a
  small test `.prg` that runs the routine on known inputs and prints `PASS`/`FAIL` to the
  screen, then run `c64-agent tests.prg --until-text PASS --frames 500`. Exit code 3
  means it never passed.
- **Guard your memory layout** with `.errorif (* > $9fff), "code too big"` and check
  alignment with `.assert`. KickAssembler then reports the problem at build time.

## Common 6502 / C64 bugs to look for

- Forgetting `sta $d019` in a raster IRQ: the IRQ fires again at once and the main
  loop starves.
- Writing text to screen RAM with a PETSCII encoding (characters look wrong): use
  `.encoding "screencode_mixed"` for screen RAM and `petscii_*` for `jsr $ffd2`.
- Colour RAM is at `$d800`, fixed. Moving the screen doesn't move colour RAM.
- Sprite pointers sit at screen + `$3f8`. Data must be 64-byte aligned and inside the
  current VIC bank (`$dd00`). The ROM charset is only visible to the VIC in banks 0 and 2.
- Sprite X above 255 needs `$d010`.
- Branches reach only −128…+127 bytes. KickAssembler reports
  `relative address is illegal (jump distance is too far: N)`; invert the branch
  around a `jmp`.
- Using `$01 = $35` (KERNAL off) but still writing the `$0314` vector or calling `$ffd2`.
- Zero page outside `$02` and `$fb-$fe` belongs to BASIC/KERNAL unless you bank them out.
- `;` is **not** a comment in KickAssembler. Use `//`.

## Hardware reference

[reference/c64-hardware.md](reference/c64-hardware.md) covers the memory map and `$01`
banking, the VIC-II registers, sprites, raster timing, colour codes, an IRQ template,
SID, CIA joystick and keyboard, KERNAL routines, and PETSCII versus screen codes. Read
it before you guess at an address.
