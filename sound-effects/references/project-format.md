# The project file (schema `sound-effects/1`)

One JSON file describes a set of sounds for one game. `sfx.py validate` checks it and lists every problem with its
place and the nearest known field name. Times are in seconds and are rounded to whole samples at 48 kHz.

```json
{
  "schema": "sound-effects/1",
  "title": "Lantern Market UI",
  "variants": 4,
  "sounds": [
    {"id": "confirm", "category": "ui", "recipe": "ui.confirm", "params": {"base_hz": 660},
     "vary": {"base_hz": 20}, "trim_db": 0, "notes": "placing a stall"},
    {"id": "chime", "category": "ui", "layers": [
      {"source": {"type": "fm", "ratio": 1.4, "index": [[0, 2.0, "db"], [0.4, 0.3]]}, "start": 0, "length": 0.4,
       "pitch": 1200, "env": {"attack": 0.001, "decay": 0.399}}],
     "vary": {"pitch_cents": 25}},
    {"id": "drizzle", "category": "ambience", "recipe": "ambience.rain_light", "channels": 2, "variants": 2,
     "loop": {"seconds": 12}, "trim_db": -6, "notes": "rain on the awnings"}
  ],
  "flows": [{"name": "choose and place", "sounds": ["confirm", "chime"], "gap": 0.3}]
}
```

## The project

| Field | Default | What |
|---|---|---|
| `schema` | required | `"sound-effects/1"` |
| `title` | required | 1-80 characters |
| `seed` | from the title | whole number; every random draw (variants, noise, drops, gusts, dither) follows from it |
| `variants` | 4 | 1-16 variants of each sound |
| `channels` | 1 | 1 (mono, the usual choice for positioned sounds) or 2 |
| `level` | `{"mode": "match", "ceiling_dbtp": -1, "limit_db": 6}` | the level step (references/checks.md): `limit_db` (0-12) is the most a peaky file's highest peaks may be limited to let the set rise; `target_lufs` (-60 to 0) holds the set at that loudness, so changing one sound's `trim_db` moves only that sound (without it the set rises as far as its peaks allow, and trimming the sound that sets the level lifts all the others) |
| `style` | none | the game's shared sound: `space` (`{"size", "decay", "mix"}`, a room every sound without a room of its own gets) and `tilt_db` (-12 to 12, a brighter or darker balance added to every sound's chain); a sound opts out with `"style": false` |
| `sounds` | required | at least one sound |
| `flows` | none | short sequences of one-shots for the audition page: `name`, `sounds` (ids), `gap` (seconds between one sound's end and the next one's start, default 0.25; -2 to 2, negative to overlap them, as a hit lands inside its swing) |
| `notes` | | free text |

## A sound

| Field | Default | What |
|---|---|---|
| `id` | required | lower-case letters, digits and `_`, starting with a letter, at most 32; file names come from it |
| `category` | required | `ui`, `retro`, `scifi`, `magic`, `impact`, `foley` (footsteps, doors, cloth, handled objects), `ambience`. It groups the page, an interface sound is held to an immediate onset, and the Unity export suggests a compression by it |
| `use` | the recipe's, else `one_shot` | `one_shot`, or `loop` for a sound that plays on from its end into its start |
| `loop` | `{"seconds": 12}` | loops only: the loop's length, 1-60 seconds |
| `recipe` + `params` | | a recipe and any of its parameters (the rest take their defaults; `sfx.py recipes` lists them) |
| `layers` + `chain` | | or the sound written out: its layers and its chain (below) |
| `vary` | the recipe's | for a recipe: parameter names and amounts; for layers: `pitch_cents`, `time_pct` (not in a loop), `cutoff_cents`, `gain_db`, and in a loop `scatter_s`: each event (not a bed) starts up to that many seconds earlier or later in each variant, so two variants of a flickering light do not flicker together |
| `variants`, `channels` | the project's | per-sound override |
| `trim_db` | the recipe's, else 0 | -24 to +6: the sound's level against the rest of the set |
| `crackle` | the recipe's, else false | true: the sound crackles on purpose (debris, sparks, static), so its isolated clicks are not flagged |
| `peak_at` | none | seconds: where the sound's main event should be loudest; the check flags a first variant whose loudest millisecond lies more than 20 ms away (a swell or a later layer louder than the hit meant to lead) |
| `style` | `true` | `false` keeps the project's style off this sound |
| `notes` | | free text, shown on the audition page |

The first variant always keeps the written values; each other variant draws its values within the amounts, from a
stream seeded by the project's seed, the sound's id, the variant and the value's name. For a sound written as layers,
`pitch_cents`, `time_pct` and `cutoff_cents` are one draw for the whole sound, so a figure keeps its intervals, and
`gain_db` is drawn for each layer, so the balance between them shifts a little. Changing one sound therefore
changes no other sound's draws (the shared level step can still move every file by one gain).

## A layer

A layer runs source, filter, drive, envelope, gain and, in a stereo sound, its placement.

| Field | What |
|---|---|
| `source` | what makes the sound (next section); `type` is required |
| `start`, `length` | seconds (up to 30, or 60 in a loop); `length` is required |
| `pitch` | Hz, a number or a curve; required for `osc`, `fm`, `chip` and `modal` (for `modal` the lowest mode, its first value) |
| `vibrato` | `{"rate_hz": 6, "depth_cents": 20}` |
| `tremolo` | `{"rate_hz": 60, "depth": 0.6}`: the level flutters between 1 and 1 - `depth` at `rate_hz` (0.1-200). Slow, a pulse; at 30-150 Hz, roughness (references/sound-design.md, section 5) |
| `env` | a curve of gain, or `{"attack", "hold", "decay", "curve"}`; an envelope that does not end at 0 is closed by a 2 ms fade |
| `filter` | `{"type": "lowpass" / "highpass" / "bandpass" / "notch", "cutoff": Hz or a curve, "q": 0.707}` |
| `drive` | 0-1 soft saturation |
| `gain_db` | the layer's level |
| `pan` | -1 (left) to 1 (right), constant-power; for a layer with a spread it balances the two channels |
| `spread` | `noise`, `drops` and `wind` in a stereo sound: 0 renders one signal in both channels, 1 two independent ones (noise and the wind's wake drawn twice, each bubble placed on its own between the speakers) |

A curve is a number or a list of points `[time, value]` or `[time, value, shape]`, the shape naming how the segment to
the next point moves: `lin`, `exp` (for frequencies), `db` (for gains, down to -80 dB and to 0 at a 0 point), `cos`
(smooth) or `hold`. Pitch and cut-off curves default to `exp`, others to `lin`.

## Sources

| `type` | Fields (defaults) | What |
|---|---|---|
| `osc` | `wave` (`sine`, `triangle`, `saw`, `square`, `pulse`), `duty` (0.5), `phase` (0 degrees) | waves built from their partials, none above 20 kHz, so sweeps do not alias |
| `fm` | `ratio` (1), `index` (1, may be a curve), `phase` | two-operator FM (Chowning 1973), computed at four times the rate |
| `noise` | `color` (`white`, `pink`, `brown`) | seeded noise (pink and brown shaped from 20 Hz up) |
| `chip` | `wave` (`pulse`, `triangle`, `noise`), `duty` (one of 0.125, 0.25, 0.5, 0.75), `mode` (`long` or `short` noise), `step_hz` (60), `levels` (16), `quantize` (true) | voices after the NES's sound hardware (NESdev Wiki): pitch and envelope change in steps of 1/`step_hz` s, the envelope takes `levels` volume steps (a curve that falls in dB reaches the lowest step and stops about a third of the way through, so give a chip voice a linear fall), and `quantize` moves pitches to those the console's timers make. For `noise`, `pitch` is the shift register's clock rate (400 Hz to 450 kHz), moved to the nearest of its 16 rates |
| `modal` | `material` (`rubber`, `wood`, `glass`, `steel`) or `t_d` (1-3000), `modes` (6, up to 16), `hardness` (0.5), `detune_cents` (0), `shape` (`bar`) or `ratios` | decaying modes with decay times t_d / f (Klatzky, Pai and Krotkov 2000 give t_d for each material), struck by a contact pulse that gets shorter and brighter with `hardness`. `shape`: `bar` (a bar free at both ends, ratios (2n+1)^2/9) or `membrane` (an ideal circular membrane, the Bessel zeros j_mn / j_01 of Abramowitz and Stegun's Table 9.5); `ratios`: 1-16 rising ratios to the lowest mode (from 1 to 64), for an object of your own design |
| `instrument` | `soundfont` (`default`), `program` (0), `bank` (0), `drums` (false), `notes` (required), `cents` (0) | a recording: `notes` (1-64 of `[time s, MIDI pitch, length s, velocity 1-127]`) played on a sample bank of music-production's configuration by its FluidSynth, dry (its reverb and chorus off), `cents` moving the result in pitch. `soundfont` names a bank of that configuration (`default` is its own); only banks whose licence puts no condition on the sound may be used (CC0, the University of Iowa recordings, the Unlicense, MIT), so the result needs no credit. Needs `render --config` |
| `sample` | `library` (required), `file` (required), `offset` (0), `cents` (0), `reverse` (false), `denoise` (none), `time_stretch` (none) | a recording of a sound library (`sfx.py samples`: FSD50K's CC0 recordings, or packs a person imported, whose licence the sound then carries): `library` is a pack's id (`fsd50k-eval`), `file` a recording's name (for FSD50K its Freesound number), or a list of 1-16 takes of which variant n plays take n mod k (the first variant the first take), so repeated sounds change recording; a take is a name or `{"file": name, "offset": seconds, "gain_db": dB, "denoise": {...}, "library": pack}` (`library` when it comes from another pack than the layer's), a take that starts at its own time (the steps of one recording of someone walking) and, since recordings arrive at the level of their loudest moment, at its own level (`samples --events` prints takes to paste). The whole recording comes at a peak of 0.5 before the layer's gain, resampled to 48 kHz; `offset` starts it that many seconds in (seconds of the recording itself, whatever `cents` and `time_stretch` then do), `reverse` plays it backwards and comes first, so with it `offset` counts from the recording's end (to play back from moment `t` of a `d`-second recording towards its beginning, `offset` is `d - t`, for `t` above 0), `cents` (-4800 to 4800, four octaves either way) moves it in pitch by resampling (a recording played higher is also shorter, one played lower longer: varispeed, as with tape), `denoise` (`{"noise": [start, end], "reduce_db": 20, "over": 3}`) subtracts the noise spectrum of a stretch of the recording that holds only noise (seconds of the recording, at least 0.1 s; `sfx.py samples --map` shows its quietest stretch), leaving the noise about `reduce_db` (3-40) under where it was with an over-subtraction of `over` (1-6), so that a part of the sound close to the noise's level can lose more than that; takes from different recordings each carry their own, since a stretch of noise in one is a moment of sound in another. `time_stretch` (`{"factor": 2, "method": "phase-locked", "keep_attack_ms": 0}`) makes it `factor` (0.25-16) times longer without a change of pitch, before `cents`: `phase-locked` (the phase vocoder with identity phase locking) for sustains and rings, `smear` (random phases) for smooth drones and textures at two times and more (both channels take the same phases, so a wide stereo recording comes out narrower: for width, pan two takes apart); `keep_attack_ms` plays the first milliseconds unstretched, so a strike stays sharp. A mono sound takes the louder channel. Needs a sample library (`render --samples LIBRARY`, or the default one) and, for Ogg, FLAC or MP3 files, FFmpeg. CC0, public-domain and the user's own recordings need no credit; the conditions of any other licence (credit, share-alike, non-commercial) carry over to the sound, and the render, its audition page and each export say which |
| `drops` | `rate` (1000 bubbles per second), `r_min_mm` (0.2), `r_max_mm` (3), `size_slope` (5), `gain_slope` (1.5), `depth` (10), `rise` (0.1), `rise_cutoff` (0.9) | a population of bubbles (van den Doel 2004): each rings at 3 / r Hz (r in metres) and decays at 0.13 / r + 0.0072 r^-1.5 per second; 50 radii from `r_min_mm` to `r_max_mm` share the rate in proportion to r^-`size_slope` (above about 5 sounds like rain, 0 to 3 like streams), gain D r^`gain_slope` (lower sounds closer), D = rnd^`depth`; bubbles with D above `rise_cutoff` rise in pitch by f (1 + `rise` d t). Rates up to about 1000 sound like dripping or light rain, 10 000 dense, 100 000 like heavy rain |
| `wind` | `band_hz` (200), `turbulence` (0.2), `gust_hz` (0.2), `whistle_hz` (0, none), `whistle_db` (-12), `whistle_q` (60) | air past obstacles (Selfridge, Moffat and Reiss 2017): wake noise from `band_hz` up (brown noise above it), its intensity growing with the eighth power of the speed, and an optional Aeolian whistle (`whistle_q` sets how narrow it is) growing with the sixth power, with a drag tone an octave up; both frequencies follow the speed. The speed is this skill's gust model: 1 + `turbulence` x a smooth random curve of unit variance whose changes stay below about `gust_hz` |

`noise`, `drops` and `wind` come at an RMS of about 0.25 (-12 dBFS) before the layer's gain, so layers mix
predictably; the level step sets the file's level.

## The chain

After the layers are summed, each stage the sound names runs once, always in this order:

| Stage | Fields |
|---|---|
| `console` | `model`: `nes` (first-order high-pass at 90 and 440 Hz, low-pass at 14 kHz) or `famicom` (high-pass at 37 Hz), the consoles' output filters as the NESdev Wiki gives them |
| `eq` | `low_cut_hz`, `high_cut_hz` (two-pole), `peak`: `{"hz", "gain_db", "q"}` |
| `tilt` | `db` (-12 to 12): a low shelf of -db/2 and a high shelf of +db/2 at 1 kHz (the Audio EQ Cookbook's shelves): brighter above 0, darker below |
| `saturation` | `drive` 0-1, `mix` 0-1 |
| `compressor` | `threshold_db` (-18), `ratio` (4), `knee_db` (6), `attack_ms` (5), `release_ms` (80), `makeup_db` (0), `mix` (1): a feed-forward compressor with a soft knee (the gain computer and smoothing of Giannoulis, Massberg and Reiss 2012). A slow attack lets the transient through and sharpens it; only an attack clearly shorter than the transient holds it down (sound-design.md, section 3) |
| `delay` | `time` (required), `feedback` up to 0.95, `mix`, `damping_hz` |
| `room` | `size` 0.2-3, `decay` seconds (the time to fall 60 dB), `mix`: a feedback delay network of eight lines (a Hadamard matrix, a low-pass in each loop), long enough at size 1 for Schroeder's density of modes up to about 2.5 s |
| `fade` | `in`, `out` seconds (not in a loop) |

`chain` is an object with one entry per stage:

```json
{"chain": {"eq": {"low_cut_hz": 80, "peak": {"hz": 2500, "gain_db": 3, "q": 1.2}},
           "compressor": {"threshold_db": -20, "ratio": 3, "attack_ms": 8, "release_ms": 90},
           "room": {"size": 1.2, "decay": 0.7, "mix": 0.15}}}
```

Every sound then passes a first-order high-pass at 10 Hz, below hearing, which takes out any DC (radiated sound has
none). A one-shot then ends 5 ms after its last sample within 70 dB of its peak, faded to silence.

## Loops

A sound with `"use": "loop"` is one period of a loop of `loop.seconds`, written as a WAV file whose `smpl` chunk marks
the whole file as a forward loop. Its period joins its own start by construction:

- A layer that starts at 0 and lasts the whole loop is a bed: its source, filter and drive run circularly (noise shaped
  over the period, bubbles and filter tails wrapping round, a moving filter run in over the period's end first). A bed
  can be `noise`, `drops`, `wind`, `osc` or `fm`; tones are tuned to a whole number of cycles per loop (they move by
  less than 1 / (2 x seconds) Hz), and vibrato and tremolo to whole cycles too. Its `pitch`, `env`, filter `cutoff` and FM `index`
  curves must end on the value they start with; an `env` shape (attack and decay) belongs to events.
- Any other layer is an event: rendered as in a one-shot and folded onto the loop, so what runs past the end comes in
  at the start. It must start inside the loop.
- The chain and the 10 Hz high-pass run circularly too; a loop is never trimmed and takes no `fade`.

The level step matches a loop by its integrated loudness rather than its loudest 200 ms, and the check
measures how its end meets its start (references/checks.md). Loops cannot be part of a flow.
