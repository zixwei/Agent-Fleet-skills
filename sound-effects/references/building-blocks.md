# Writing a set with a script (`scripts/sfxkit.py`)

For more than a few sounds, write a short Python script that builds the project instead of writing its JSON by hand.
`sfxkit` holds the pieces most sounds are made of. Each returns plain project JSON, so the file the script writes stays
readable and every value can still be changed in it. The default times and levels are this skill's starting points.

```python
import sys
sys.path.insert(0, 'PATH/TO/sound-effects/scripts')
from sfxkit import *

STEPS = [take('118985', 1.702), take('118985', 2.204, gain=-2), take('118985', 3.338, gain=1)]   # from samples --events

sounds = [
    sound('step_wood', 'foley', 'A boot on old boards: each variant its own step, with a little weight.', [
        rec('fsd50k-eval', STEPS, 0.0, 0.30, filt=hp(120)),
        thump(0.004, 130, 62, 0.10, gain=-20),
    ], variants=6, vary={'pitch_cents': 35, 'gain_db': 1.0}, trim_db=-6),

    sound('door_slam', 'foley', 'A heavy door: the recorded slam over low weight, in a stone hall.', [
        rec('fsd50k-dev', '216872', 0.0, 0.5, offset=0.02),
        thump(0.01, 110, 48, 0.25, gain=-6),
        rumble(0.0, 120, 0.6, gain=-14),
    ], chain={'room': {'size': 1.2, 'decay': 0.9, 'mix': 0.16}}),

    sound('rain', 'ambience', 'Rain on a roof, a seamless stereo loop.',
          looped('fsd50k-eval', '119500', 12.0, 2.0, (0.2, 3.6), filt=hp(120), pan=-0.6)
          + looped('fsd50k-eval', '119500', 12.0, 2.0, (1.1, 4.4), filt=hp(120), pan=0.6),
          use='loop', loop={'seconds': 12}, channels=2, variants=1),
]
save('project.json', 'My game', sounds, variants=3, flows=[{'name': 'walk', 'sounds': ['step_wood', 'step_wood'], 'gap': 0.3}])
```

`save` checks the project and writes it. A mistake raises an error that says what is wrong and where, and writes nothing.
Then render as usual: `python scripts/sfx.py render project.json --output render_1`.

## Starting stacks

Where to begin for sounds no recipe covers. These are this skill's starting points, to change for the game and by what
the check and the listener say:

| Sound | Stack |
|---|---|
| Footsteps | one recorded walk as takes (`samples --events PACK/FILE --min-gap 0.3 --even`: one take per step, evened) over a short `thump` for weight; `vary` pitch a little |
| Sword swing | a `whoosh` sweeping up and back, with a thin high layer for the blade; a recording of a swish if one fits |
| Blade on a body | a `crack`, a short band-passed noise for flesh and a `thump`, with a thin metallic ring from `modal` kept far under them |
| Hurt voice | a recording of a person (`samples --find grunt`, `breathing`, `scream`), pitched a little; check the voice suits the character |
| Drinking | a few recorded gulps as takes, or band-passed `noise` bursts at a swallow's pace, then a short breath |
| Chest or crate | a recorded wood creak and a latch as separate events, a `thump` where the lid lands |
| Coins | several short `modal` hits with long `t_d` and high, inharmonic `ratios`, or recorded coins as takes |
| Door | the recorded creak and the slam as separate layers at their own times, a `thump` and a `rumble` under the slam, a `room`; give the slam's time as `peak_at` |
| Heavy metal hit | a recorded strike or `modal` with a long `t_d` over a `thump`, with a `crack` at the contact |
| Engine or machine (loop) | a bed of a `saw` or `pulse` tone at its firing rate through a low-pass, a bed of brown noise, a slow `tremolo`, and a recorded engine `looped` over them; harmonics carry it on small speakers |
| Electrical hum or flicker (loop) | a bed of tones at the mains frequency and its multiples (50 or 60 Hz), with short noise events for the flicker and `vary.scatter_s` so variants differ |
| Long ambience (loop) | a synthesized bed (`noise`, `wind`) for the whole loop, with short recordings placed as events at their own times and `vary.scatter_s`; two takes of one recording (`looped`) need `seconds / 2 + fade` of it each. The check flags an event the bed drowns (`buried_event`) |
| Breathing | a recording pitched down, or band-passed `noise` under an in-and-out envelope, with a gap between breaths |
| Explosion | a `crack`, a noise burst falling in cut-off, a `thump` and a long `rumble`, in a `room` |

## Recordings

| Builder | What it makes |
|---|---|
| `rec(pack, file, start, length, offset=0, cents=0, gain=0, env=None, filt=None, reverse=False, pan=None, denoise=None, stretch=None, tremolo=None)` | a layer playing a recording. `file` is a name or a list of takes, one per variant. Without `env`, full level between short fades. With `reverse`, `offset` counts from the recording's end |
| `take(file, offset=0, gain=0, denoise=None, library=None)` | one take of that list. Recordings arrive at the level of their loudest moment, so give each take its own gain. `samples --events PACK/FILE` prints a list to paste |
| `looped(pack, file, seconds, fade, offsets, cents=0, gain=0, filt=None, pan=None, stretch=None)` | the two layers of a seamless loop from one recording (sound-design.md, section 6). The sound needs `use='loop'` and `loop={'seconds': ...}` |
| `lands(event, offset, cents, when)` | the `start` that makes the recording's moment `event` play at `when` |
| `speed(cents)` | how much faster a recording moved by `cents` plays |

## Synthesis

| Builder | What it makes |
|---|---|
| `tone(start, length, pitch, wave='sine', env=None, gain=0, filt=None, vibrato=None, tremolo=None)` | an oscillator; `pitch` is Hz or a curve `[[time, Hz], ...]`. Without `env` it decays, except as a bed: a layer lasting a whole loop stays at full level |
| `noise(start, length, color='pink', env=None, filt=None, gain=0, spread=None)` | white, pink or brown noise |
| `chip(start, length, pitch, wave='pulse', duty=0.5, env=None, gain=0, filt=None, step_hz=60)` | a chip voice; without `env` it falls evenly, because a fall in dB runs out of its 16 volume steps early |
| `fm(start, length, pitch, ratio=1, index=1, env=None, gain=0, filt=None)` | two-operator FM; `index` may be a curve |
| `wind(start, length, band_hz=200, turbulence=0.2, gust_hz=0.2, env=None, gain=0, filt=None, spread=None)` | air past obstacles |
| `modal(start, length, hz, t_d, modes=6, hardness=0.8, gain=0, detune=15, ratios=None)` | a struck object: each mode falls to 1/e in t_d / f seconds, so a long `t_d` reads as metal or glass and a short one as wood or rubber (sound-design.md, section 4); `ratios` for an object of your own design |
| `thump(start, high, low, length, gain=0, glide=0.08)` | low weight under a hit: a sine falling from `high` to `low` Hz |
| `rumble(start, cutoff, length, gain=0, attack=0.01)` | low mass: brown noise under a low-pass, dying away |
| `crack(start, gain=-6, cutoff=2500, length=0.008)` | the contact at the front of a hit |
| `whoosh(start, length, low, high, gain=0, q=1.5, color='pink', peak=0.6)` | air moving past: noise through a band-pass that sweeps up and back |

## Shapes and filters

| Builder | What it makes |
|---|---|
| `clip(length, fade_in=0.003, fade_out=0.04)` | an envelope at full level between two short fades |
| `decay(length, attack=0.002)` | a fast rise, then a fall to silence |
| `swell(length, top=0.85)` | a rise to `top` of the length and a quick close, for a reversed sound running into a hit |
| `equal_power(length, fade)` | equal-power fades at both ends, for crossfading unrelated takes |
| `hp(hz, q=0.7071)`, `lp(hz, q=0.7071)`, `bp(hz, q=1)` | a layer's filter |

## The project

| Builder | What it makes |
|---|---|
| `sound(id, category, notes, layers, chain=None, **extra)` | one sound; `extra` takes `channels`, `variants`, `trim_db`, `vary`, `use`, `loop`, `peak_at`, `style`, `crackle` |
| `save(path, title, sounds, **extra)` | checks the project and writes it; `extra` takes `variants`, `seed`, `style`, `flows`, `level`, `notes` |
