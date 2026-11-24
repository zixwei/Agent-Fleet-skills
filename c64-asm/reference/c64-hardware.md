# C64 hardware cheat sheet (PAL)

## Memory map

| Range         | Default contents                                       |
| ------------- | ------------------------------------------------------ |
| `$0000-$0001` | CPU port: `$00` direction, `$01` memory configuration  |
| `$0002-$00ff` | Zero page. `$02` and `$fb-$fe` are free; the rest is used by BASIC/KERNAL (free if you never return to BASIC and keep KERNAL calls to a minimum) |
| `$0100-$01ff` | Stack                                                  |
| `$0200-$03ff` | KERNAL/BASIC work area: `$0277` keyboard buffer, `$0314/15` IRQ vector, `$0318/19` NMI vector |
| `$0400-$07e7` | Default screen RAM (40×25). `$07f8-$07ff` = sprite pointers |
| `$0801-$9fff` | BASIC program area: free RAM for your program          |
| `$a000-$bfff` | BASIC ROM (RAM underneath)                             |
| `$c000-$cfff` | Free RAM (4 KB)                                        |
| `$d000-$d3ff` | VIC-II registers (repeat every 64 bytes)               |
| `$d400-$d7ff` | SID registers                                          |
| `$d800-$dbe7` | Colour RAM (low 4 bits only, fixed address)            |
| `$dc00-$dcff` | CIA 1: keyboard, joysticks, IRQ timer                  |
| `$dd00-$ddff` | CIA 2: VIC bank, serial bus, NMI timer                 |
| `$e000-$ffff` | KERNAL ROM (RAM underneath)                            |

`$01` banking (bits 0–2): `$37` default (BASIC+IO+KERNAL) · `$36` IO+KERNAL, RAM at
`$a000` · `$35` IO only, RAM at `$a000` and `$e000` (then the hardware vectors at
`$fffa-$ffff` are used, not `$0314`) · `$34` all RAM (no IO). The CPU always *writes*
through to the RAM under ROM.

## VIC-II

| Reg           | Purpose                                                          |
| ------------- | ---------------------------------------------------------------- |
| `$d000-$d00f` | Sprite 0–7 X (low 8 bits) / Y pairs: `$d000`=X0 `$d001`=Y0 …     |
| `$d010`       | Sprite X bit 8 (one bit per sprite)                              |
| `$d011`       | b7 raster bit 8 · b6 ECM · b5 bitmap · b4 display enable · b3 25 rows · b0-2 Y scroll. Default `$1b` |
| `$d012`       | Raster line (read) / raster IRQ compare line (write)             |
| `$d015`       | Sprite enable                                                    |
| `$d016`       | b4 multicolour · b3 40 columns · b0-2 X scroll. Default `$c8`    |
| `$d017`/`$d01d` | Sprite double height / double width                            |
| `$d018`       | b4-7 screen RAM offset (×`$400`) · b1-3 charset offset (×`$800`) / b3 bitmap offset (×`$2000`). Default `$15` (screen `$0400`, ROM uppercase charset) |
| `$d019`       | Interrupt latch: write `$ff` (or `asl $d019`) to acknowledge     |
| `$d01a`       | Interrupt enable: b0 raster, b1 sprite-background, b2 sprite-sprite |
| `$d01b`       | Sprite priority (1 = behind background)                          |
| `$d01c`       | Sprite multicolour enable                                        |
| `$d01e`/`$d01f` | Sprite-sprite / sprite-background collision (cleared on read)  |
| `$d020`/`$d021` | Border / background colour · `$d022-$d024` extra background colours |
| `$d025`/`$d026` | Sprite multicolour 0 / 1 · `$d027-$d02e` sprite 0–7 colours    |

**VIC bank** is set by `$dd00` bits 0–1 (inverted): `%11` bank 0 `$0000` (default) ·
`%10` `$4000` · `%01` `$8000` · `%00` `$c000`. The VIC sees the ROM character set at
`$1000-$1fff` in banks 0 and 2 only. Screen = bank + (`$d018`>>4)×`$400`. Sprite
pointers live at screen + `$3f8`, and pointer × 64 = sprite data address within the bank.

**Sprites:** 24×21 pixels, 63 bytes (3 per row), 64-byte aligned. Visible screen
origin: X = 24, Y = 50. The 320×200 area spans X 24–343 and Y 50–249.

**Timing (PAL):** 312 raster lines, 63 cycles per line, 19656 cycles per frame, 50.12 Hz.
The text area covers lines 51–250. A **badline** (every 8th line in the text area)
steals 40 cycles, and each active sprite steals more. Do raster splits on stable lines,
and use a raster IRQ at line 250+ for per-frame game logic.

**Colours:** 0 black · 1 white · 2 red · 3 cyan · 4 purple · 5 green · 6 blue · 7 yellow ·
8 orange · 9 brown · 10 light red · 11 dark grey · 12 grey · 13 light green · 14 light blue ·
15 light grey.

## Raster IRQ setup (KERNAL banked in)

```asm
        sei
        lda #$7f
        sta $dc0d        // CIA1 IRQs off
        lda $dc0d        // ack
        lda #<irq
        sta $0314
        lda #>irq
        sta $0315
        lda #250
        sta $d012
        lda $d011
        and #$7f
        sta $d011        // raster bit 8 = 0
        lda #1
        sta $d01a        // raster IRQ on
        cli
irq:    lda #$ff
        sta $d019        // ack, or the IRQ fires again at once
        …
        jmp $ea81        // restore registers + RTI ($ea31 also scans the keyboard)
```

With `$01 = $35` (KERNAL out), set `$fffe/$ffff` instead, and save and restore A/X/Y yourself before `rti`.

## SID (`$d400`)

Voice 1 `$d400-$d406`, voice 2 `+7`, voice 3 `+14`: freq lo/hi, pulse width lo/hi,
control (b0 gate · b4 triangle · b5 saw · b6 pulse · b7 noise), attack/decay,
sustain/release. Filter: `$d415-$d416` cutoff, `$d417` resonance and routing,
`$d418` b0-3 master volume, b4-6 filter mode. `$d41b` is voice-3 oscillator output
(a cheap random source when voice 3 plays noise).

## CIA 1 (`$dc00`): joystick and keyboard

- **Joystick port 2 → `$dc00`**, port 1 → `$dc01`. Active low: b0 up, b1 down, b2 left,
  b3 right, b4 fire. `lda $dc00 / and #$10 / beq firePressed`.
- **Keyboard:** write the column mask to `$dc00` (0 = select), read the rows from `$dc01`
  (0 = pressed). Port-1 joystick input looks like key presses. Example: SPACE is column 7,
  row 4, so write `%01111111` to `$dc00` and check bit 4 of `$dc01`.
- `$dc0d`: interrupt control. Write `$7f` to disable all; read to acknowledge.

## KERNAL routines (KERNAL must be banked in)

| Address | Name   | Does                                            |
| ------- | ------ | ----------------------------------------------- |
| `$ffd2` | CHROUT | Print the PETSCII character in A                |
| `$ffe4` | GETIN  | A = key from the keyboard buffer (0 = none)     |
| `$ffcf` | CHRIN  | Read a line of input                            |
| `$fff0` | PLOT   | C=0: move the cursor to row X, column Y         |
| `$e544` |        | Clear the screen                                |
| `$ffba` / `$ffbd` / `$ffd5` / `$ffd8` | SETLFS / SETNAM / LOAD / SAVE | File I/O |
| `$ea31` / `$ea81` |  | IRQ exit: full KERNAL handler / just restore and RTI |

## PETSCII vs screen codes

Screen RAM holds **screen codes** (`@`=0, `A`=1 … `Z`=26, space=32, `0`=48), and bit 7
selects reverse video. CHROUT takes **PETSCII** (`A`=65, RETURN=13, clear screen=147,
cursor home=19). In KickAssembler, choose the matching `.encoding`.
