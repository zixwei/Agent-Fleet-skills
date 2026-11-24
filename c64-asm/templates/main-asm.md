# Starter project: src/main.asm

A tested KickAssembler starting point: a BASIC stub, a raster IRQ that sets a
once-per-frame flag, a main loop locked to that flag, and a joystick-driven sprite.

To use it, write the code block below to `src/main.asm` exactly as it is (everything
between the fences, nothing else), then build it as described in SKILL.md.

```asm
// ─────────────────────────────────────────────────────────────────────────────
// main.asm — KickAssembler starter: raster IRQ, frame-locked game loop,
// joystick-driven sprite. Build and run:
//   java -jar "$KICKASS_JAR" src/main.asm -o "$PWD/build/main.prg" -odir "$PWD/build" -symbolfile -vicesymbols
//   npx -y -p c64-ready@3.3.1 c64-agent build/main.prg --symbols build/main.sym --frames 100 --screenshot shot.png
// ─────────────────────────────────────────────────────────────────────────────

// ── constants ────────────────────────────────────────────────────────────────
.const SCREEN      = $0400
.const COLOR_RAM   = $d800
.const SPRITE_PTRS = SCREEN + $03f8
.const SPRITE_DATA = $2000          // must be 64-byte aligned and inside VIC bank 0
.const IRQ_LINE    = 250            // raster line for the frame IRQ (below the screen)

.const frameFlag   = $02            // zero page: set by the IRQ once per frame

// ── BASIC stub: 10 SYS start ─────────────────────────────────────────────────
BasicUpstart2(start)

// ── entry ────────────────────────────────────────────────────────────────────
start:
        sei
        lda #$7f
        sta $dc0d                   // disable CIA1 interrupts (KERNAL 60 Hz timer)
        sta $dd0d                   // disable CIA2 NMIs
        lda $dc0d                   // acknowledge anything pending
        lda $dd0d

        lda #<irq                   // KERNAL is banked in, so use the $0314 vector
        sta $0314
        lda #>irq
        sta $0315

        lda #IRQ_LINE
        sta $d012
        lda $d011
        and #%01111111              // bit 7 = raster line bit 8
        sta $d011
        lda #%00000001
        sta $d01a                   // enable raster IRQ
        sta $d019                   // clear any stale raster IRQ
        cli

        jsr initScreen
        jsr initSprite

// ── main loop: runs once per frame ───────────────────────────────────────────
mainLoop:
        lda frameFlag
        beq mainLoop                // wait for the IRQ
        lda #0
        sta frameFlag

        jsr readJoystick
        jmp mainLoop

// ── raster IRQ ───────────────────────────────────────────────────────────────
irq:
        lda #$ff
        sta $d019                   // acknowledge the VIC interrupt
        inc frameFlag
        jmp $ea81                   // KERNAL: restore A/X/Y and RTI

// ── subroutines ──────────────────────────────────────────────────────────────
initScreen:
        lda #BLACK
        sta $d020                   // border
        sta $d021                   // background
        jsr $e544                   // KERNAL: clear screen
        ldx #0
!loop:  lda message,x
        beq !done+
        sta SCREEN + 40*1 + 2,x
        lda #WHITE
        sta COLOR_RAM + 40*1 + 2,x
        inx
        bne !loop-
!done:  rts

initSprite:
        lda #SPRITE_DATA / 64
        sta SPRITE_PTRS             // sprite 0 pointer
        lda #YELLOW
        sta $d027                   // sprite 0 colour
        lda #160
        sta $d000                   // sprite 0 X (low 8 bits)
        lda #140
        sta $d001                   // sprite 0 Y
        lda #0
        sta $d010                   // X bit 8 for all sprites
        lda #%00000001
        sta $d015                   // enable sprite 0
        rts

// Joystick port 2 at $dc00, active low: bit 0 up, 1 down, 2 left, 3 right, 4 fire.
// (X is kept within 0-255 here; use $d010 for the right-hand side of the screen.)
readJoystick:
        lda $dc00
        lsr
        bcs !+
        dec $d001                   // up
!:      lsr
        bcs !+
        inc $d001                   // down
!:      lsr
        bcs !+
        dec $d000                   // left
!:      lsr
        bcs !+
        inc $d000                   // right
!:      lsr
        bcs !+
        inc $d027                   // fire: cycle colour
!:      rts

// ── data ─────────────────────────────────────────────────────────────────────
.encoding "screencode_mixed"
message:
        .text "kickassembler + c64-ready"
        .byte 0

* = SPRITE_DATA "Sprite"
ship:
        .byte %00000000, %00011000, %00000000
        .byte %00000000, %00111100, %00000000
        .byte %00000000, %00111100, %00000000
        .byte %00000000, %01111110, %00000000
        .byte %00000000, %01111110, %00000000
        .byte %00000000, %11111111, %00000000
        .byte %00000001, %11111111, %10000000
        .byte %00000011, %11100111, %11000000
        .byte %00000111, %11000011, %11100000
        .byte %00001111, %11000011, %11110000
        .byte %00011111, %11111111, %11111000
        .byte %00111111, %11111111, %11111100
        .byte %01111111, %11111111, %11111110
        .byte %11111111, %11111111, %11111111
        .byte %11111100, %11111111, %00111111
        .byte %11111000, %01111110, %00011111
        .byte %11110000, %00111100, %00001111
        .byte %11100000, %00011000, %00000111
        .byte %11000000, %00000000, %00000011
        .byte %10000000, %00000000, %00000001
        .byte %00000000, %00000000, %00000000
```
