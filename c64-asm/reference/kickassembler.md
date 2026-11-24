# KickAssembler quick reference (v5.x)

Written for models that know 6502 but mix up assembler dialects. KickAssembler is **not**
ACME, ca65, DASM or 64tass. Use only the syntax on this page.

## Things that differ from other assemblers

| Thing                 | KickAssembler                               | Not this                         |
| --------------------- | ------------------------------------------- | -------------------------------- |
| Comment               | `// line` and `/* block */`                 | `; comment`: `;` **separates statements** (`lda #0 ; sta $d020` is two instructions), so a `;` comment is a syntax error or, worse, assembles |
| Set PC                | `* = $1000` or `* = $1000 "Name"`           | `org $1000`, `.org`              |
| Constant              | `.const SCREEN = $0400`                     | `SCREEN = $0400`, `equ`          |
| Address with a symbol | `.label border = $d020`                     |                                  |
| Mutable (assembly-time) | `.var i = 0`                              |                                  |
| Include               | `#import "file.asm"` (+ `#importonce` in the file) | `.include`, `!src`        |
| Binary data           | `.import binary "data.bin"`                 | `incbin`, `!bin`                 |
| Bytes / words         | `.byte 1,2,$ff` · `.word label` · `.dword`  | `!byte`, `.db`, `dc.b`           |
| Text                  | `.text "hello"` (uses the current `.encoding`) | `!scr`, `!pet`                |
| Fill                  | `.fill 40, $20` · `.fill 256, sin(toRadians(i*360/256))*127` | `!fill`, `ds`   |
| Align                 | `.align $100`                               |                                  |
| Low / high byte       | `#<label` · `#>label`                       | `#lo(label)`                     |
| Number formats        | `$ff` hex · `%1010` binary · `255` · `'a'` char |                              |

## Labels

```asm
main:   lda #0          // normal label (goes into the .sym file)
!loop:  dex             // multi-label: can repeat in the same scope
        bne !loop-      // nearest !loop before here
        beq !skip+      // nearest !skip after here
!:      inx             // anonymous multi-label
        bne !-          // previous `!`. Use !+ / !++ / !-- for more distance
!skip:
```

Braces open a scope: labels defined inside `{ … }` are local to it.
`.namespace player { … }` gives `player.x` from outside.

## BASIC stub and entry

```asm
BasicUpstart2(start)     // emits "10 SYS <start>" at $0801. Code follows at $080e
start:  …
```

If code must live elsewhere, set `* = $0801` before `BasicUpstart2`, and put
`* = $c000 "Code"` before the `start:` label. `c64-agent` RUNs `$0801` programs automatically.

## Encodings

```asm
.encoding "screencode_mixed"   // DEFAULT. Lowercase source text → screen codes 1-26 (shown as uppercase in the default charset)
.encoding "screencode_upper"
.encoding "petscii_mixed"      // for CHROUT ($ffd2) printing
.encoding "petscii_upper"
```

- `.text` written straight into screen RAM needs a `screencode_*` encoding.
- Text printed through `jsr $ffd2` needs `petscii_*`.

## Built-in constants

Colours: `BLACK WHITE RED CYAN PURPLE GREEN BLUE YELLOW ORANGE BROWN LIGHT_RED DARK_GREY
GREY LIGHT_GREEN LIGHT_BLUE LIGHT_GREY` (0–15), e.g. `lda #LIGHT_BLUE`.

## Macros, functions and pseudocommands

```asm
.macro SetBorder(color) {
        lda #color
        sta $d020
}
        SetBorder(RED)                 // call: name(args), no colon

.function screenAddr(col, row) { .return $0400 + row*40 + col }
        sta screenAddr(10, 5)

.pseudocommand mov src : dst {         // arguments separated by ':'
        lda src
        sta dst
}
        mov #$00 : $d020               // the argument keeps its addressing mode
```

Macros get fresh labels on every call, so local labels inside them are safe.

## Loops, conditions and assembly-time code

```asm
.for (var i = 0; i < 8; i++) {
        lda #i
        sta $d027 + i                  // unrolled: sprite colours
}
.if (DEBUG) { inc $d020 } else { nop }

sineTable:
        .fill 256, 128 + 127*sin(toRadians(i*360/256))
```

Useful functions: `sin cos toRadians abs floor round min max mod`, `toHexString(x)`,
`toBinaryString(x)`, `LoadBinary("f")`, `LoadSid("f")`, `LoadPicture("f.png")`.

## Assertions and debug output (shown in the build log)

```asm
.print "player at $" + toHexString(player)
.errorif (* > $9fff), "Code overflows into BASIC ROM"
.assert "table is page aligned", <sineTable, 0
```

Use `.errorif` to guard memory layout. It turns a silent overlap into a build error.

## SID music

```asm
.var music = LoadSid("music.sid")
* = music.location "Music"
        .fill music.size, music.getData(i)
// init: lda #music.startSong-1  jsr music.init     play once per frame: jsr music.play
```

## Zero page and segments

```asm
.const zpPtr = $fb                     // $fb-$fe are free for user programs
        lda (zpPtr),y

.segmentdef Data [start=$4000]
.segment Data
table:  .byte 1,2,3
.segment Default
```

## Illegal opcodes

These are available by default: `lax`, `sax`, `dcp`, `isc`, `slo`, `rla`, `sre`, `rra`, `anc`, `alr`, `arr`, `sbx`, ….
`.cpu _6502NoIllegals` turns them off, and `.cpu _65c02` switches the target.

## Build output

The build command in SKILL.md passes `-symbolfile -vicesymbols`. Useful extra flags:
`-showmem` (memory map), `-debugdump`, `-define DEBUG` (then `#if DEBUG … #endif`),
`-vicesymbols`, `-bytedump` (assembled bytes per line). Errors look like:

```
        jsr readJoystik
            ^
Error: Unknown symbol 'readJoystik'
at line 54, column 13 in main.asm
```
