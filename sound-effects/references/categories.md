# Retro, sci-fi, magic, impact and ambience recipes

What the recipes of the other categories do and the rules they encode. Each rule cites its source; the sounds
themselves (the figures, times, levels and defaults) are this skill's own design, tuned during development against
measurements of CC0 game sound libraries and judged by ear on the audition page: starting points, to be changed for
the game. `python scripts/sfx.py recipes --name retro.coin` prints any recipe with its parameters, ranges and rules.

## Retro (`retro.*`): chip voices after the NES

`jump`, `coin`, `powerup`, `shoot`, `hit`, `explosion`, `blip`, `extra_life`. They play on the `chip` source, which
behaves as the console's sound hardware does as the NESdev Wiki documents it:

| Rule | Source |
|---|---|
| Pulse waves with duty cycles of 12.5, 25, 50 or 75 % | NESdev Wiki, APU Pulse |
| Pitches only on the periods the 11-bit timers allow (f = f_cpu / (16 (t + 1)) for pulses) | NESdev Wiki, APU Pulse |
| A 32-step triangle wave (f = f_cpu / (32 (t + 1))) | NESdev Wiki, APU Triangle |
| 1-bit noise from a 15-bit shift register, long (32767 steps) or short (93 or 31) mode, at 16 clock rates | NESdev Wiki, APU Noise |
| Volume in 16 practically linear steps; the envelope decays as a stepped saw | NESdev Wiki, APU Envelope |
| Parameters change in steps (envelopes about 240 Hz, sweeps 120 Hz, the frame interrupt 60 Hz); these recipes step at 60 Hz | NESdev Wiki, APU Frame Counter |
| The consoles' output filters: high-pass at 90 and 440 Hz and low-pass at 14 kHz (NES), high-pass at 37 Hz (Famicom) | NESdev Wiki, APU Mixer |

The `console` parameter picks those filters (0 none, 1 NES, 2 Famicom, the default). The NES's 440 Hz high-pass
makes a square wave a train of spikes: thinner and peakier than the retro library this skill measured, so it is a
choice for a game that wants that console's exact colour. Waveforms are band-limited, as the console's analogue output
is, rather than folded as a naive digital copy would be.

## Impact (`impact.*`): struck objects

`punch`, `thud`, `wood_knock`, `metal_hit`, `metal_clang`, `glass_tink`, `swing`. The struck ones play on the
`modal` source; the hits are stacks of layers:

- `punch`: a low sine thump that swells over 18 ms as it falls in pitch (the loudest moment of the measured punches
  comes 20-40 ms in), pink noise in the low mids for the flesh, a band of noise for the slap, saturation as glue.
- `wood_knock`: the struck board (wood modes), the hollow body behind it (two lower modes ringing a little longer) and
  a click of contact; `hollow_db` turns a box into a solid block.
- `metal_hit`: an object's modes damped by its hold or load (`hit_td`), a faint free ring of higher modes at the steel
  decay (the cue to metal), and a bright contact; `metal_clang` is the free ring alone, long and bell-like.

| Rule | Source |
|---|---|
| A big hit is built from parts with separate jobs: an attack, a sustain and release that may come from other sounds, a low "sweetener" for weight, and detail such as debris for what broke | Metcalfe, Designer Sounds (2013); Bazil, Layers Of Complexity (2012) |
| Punch is a short period of significant change in power, tied to the energy at that moment and to the loudness around it | Fenton, Lee and Wakefield (2015) |
| Material is heard mostly in how fast the sound decays: fast for rubber and wood, slow for glass and steel | Klatzky, Pai and Krotkov (2000) |
| Each mode falls to 1/e in t_d / f; the materials use the study's regression estimates of the t_d at which half its listeners would choose them (about 10^0.46 rubber, 10^0.50 wood, 10^2.10 glass, 10^2.65 steel; it tested t_d from 3 to 300, so steel's lies beyond them) | Klatzky, Pai and Krotkov (2000) |
| Modes of a bar free at both ends lie about in the ratio (2n + 1)^2 | Russell, Flexural Vibration of a Rectangular Bar |
| A swung object's tone follows the air speed (f = St u / d) and a drag tone sits at twice the frequency, its fluctuating force about a tenth of the lift's | Selfridge, Moffat and Reiss (2017) |

The strike is a Gaussian contact pulse whose -3 dB point moves up from 300 Hz (soft) with `hardness`, until near the
hardest the pulse is shorter than a sample and passes every mode: this skill's choice (a pulse with spectral nulls made nearly equal variants differ by 11 dB in loudness), after van den
Doel, Kry and Pai (2001), for whom hardness is the force's duration and its exact shape matters little. Above a
hardness of 0.7 the strike also chatters: a burst of impulses at its first four modes within 15 ms, the micro collisions
that the same paper found to make very hard contacts convincing.

## Sci-fi (`scifi.*`) and magic (`magic.*`)

`zap`, `charge`, `shield_hit`, `teleport`, `console_beep`, `power_down`, `explosion`; `sparkle`, `cast`, `heal`,
`curse`, `pickup_chime`. Built from FM, filtered noise, waves and modes. The genre's sounds have no physical model to
follow; the rules are the general ones that hold for any short signal, and the layering of hits (above) for `zap`
(a body two octaves under the diving tone) and `explosion` (a crack, a falling boom, a blast closing from bright to
dull, a mid-range crunch, debris and a rumble that outlasts them):

| Rule | Recipes | Source |
|---|---|---|
| Upward motion for starting, opening and positive results; downward for ending and closing | `charge`, `teleport`, `cast`, `heal`, `pickup_chime`; `power_down`, `curse` | Material Design 2, Sound attributes |
| Irregular harmonics, a wide pitch range and rapid onsets draw attention | `zap`, `console_beep`, `curse` | Brewster, Wright and Edwards, citing Edworthy, Loxley and Dennis (1991) |
| A level that flutters at about 30 to 150 Hz is rough, the quality that makes screams and alarms alarming; repeated clicks were most aversive at 40 to 80 Hz | `curse` (its flutter is set in that range: this skill's choice) | Arnal et al. (2015, 2019) |
| Noise added to music makes it feel less pleasant | `curse` | Blumstein, Bryant and Kaye (2012) |
| A sound that decays slowly is heard as glass or metal rather than rubber or wood | `shield_hit`, `sparkle`, `pickup_chime` | Klatzky, Pai and Krotkov (2000) |

Grain scatters (`sparkle`, `cast`, `pickup_chime`) follow the parameters: each variant scatters differently, a render
repeats exactly, and the first grain lands on the start so the sound answers its trigger at once.

## Ambience (`ambience.*`): seamless loops

`rain_light`, `rain_heavy`, `breeze`, `wind_gusty`, each a loop (`"use": "loop"`, `loop.seconds`, 12 by default). Rain
plays on the `drops` source, wind on the `wind` source; every layer lasts the whole loop, so the loop joins its start.

| Rule | Source |
|---|---|
| Up to about 1000 bubbles per second sounds like dripping or light rain, up to 10 000 dense with single drops still heard, up to 100 000 blended like heavy rain; more makes no audible difference | van den Doel (2004) |
| Bubble rates falling as 1/r^gamma with gamma above about 5 sound very much like rain (0 to 3 like streams); a smaller gain exponent sounds closer, a larger one more distant | van den Doel (2004) |
| The wake of an obstacle gives wide-band noise from its Aeolian frequency up, growing with the eighth power of the air speed; both move with the speed | Selfridge, Moffat and Reiss (2017) |
| A thin obstacle sings a narrow Aeolian tone, growing with the sixth power of the speed, with a drag tone an octave up | Selfridge, Moffat and Reiss (2017) |

The rain's wash and rumble, and the wind's gusts (a smooth random speed whose spread is `turbulence` and whose pace is
`gust_hz`), are this skill's own design. Because the wake grows with the eighth power of the speed, a gust of a quarter
more speed is about 8 dB louder: watch the loudness range and the halves on the audition page, and lengthen the loop or
lower `turbulence` if one swell repeats too plainly.

## Sources

- NESdev Wiki: APU Pulse, APU Triangle, APU Noise, APU Envelope, APU Frame Counter, APU Mixer.
- Klatzky, R. L., Pai, D. K. and Krotkov, E. P. (2000). Perception of material from contact sounds. *Presence* 9(4).
- Russell, D. A. Flexural vibration of a rectangular bar. Penn State, Acoustics and Vibration Animations.
- Selfridge, R., Moffat, D. and Reiss, J. D. (2017). Sound synthesis of objects swinging through air using physical
  models. *Applied Sciences* 7(11), 1177.
- van den Doel, K. (2004). Physically-based models for liquid sounds. *Proceedings of ICAD 04*.
- Material Design 2: Sound attributes.
- Metcalfe, N. (2013). Designer Sounds. *Sound On Sound*, October 2013.
- Bazil, E. (2012). Layers Of Complexity. *Sound On Sound*, November 2012.
- Fenton, S., Lee, H. and Wakefield, J. (2015). Hybrid multiresolution analysis of 'punch' in musical signals. *AES
  138th Convention*, paper 9229.
- Brewster, S. A., Wright, P. C. and Edwards, A. D. N. *Guidelines for the Creation of Earcons*. University of Glasgow.
- van den Doel, K., Kry, P. G. and Pai, D. K. (2001). FoleyAutomatic: physically-based sound effects for interactive
  simulation and animation. *Proceedings of ACM SIGGRAPH 2001*.
- Arnal, L. H., Flinker, A., Kleinschmidt, A., Giraud, A.-L. and Poeppel, D. (2015). Human screams occupy a privileged
  niche in the communication soundscape. *Current Biology* 25(15), 2051-2056.
- Arnal, L. H., Kleinschmidt, A., Spinelli, L., Giraud, A.-L. and Mégevand, P. (2019). The rough sound of salience enhances
  aversion through neural synchronisation. *Nature Communications* 10, 3671.
- Blumstein, D. T., Bryant, G. A. and Kaye, P. (2012). The sound of arousal in music is context-dependent. *Biology
  Letters* 8(5), 744-747.
