# Designing a game's sounds

The recipes are starting points. A game's sounds come from its brief: its world, its materials, its style and what
each sound has to tell the player. This page is how to design them: by changing a recipe's parameters, by expanding
a recipe into layers and changing those, or by writing layers from nothing. The rules it states cite their sources;
figures marked as this skill's are its own design or its own measurements.

## Contents

1. [From the brief to a palette](#1-from-the-brief-to-a-palette)
2. [A recipe, an expanded recipe or layers](#2-a-recipe-an-expanded-recipe-or-layers)
3. [A sound as a stack of layers](#3-a-sound-as-a-stack-of-layers)
4. [Material, size and force](#4-material-size-and-force)
5. [Meaning and motion](#5-meaning-and-motion)
6. [Recorded layers](#6-recorded-layers)
7. [Variants](#7-variants)
8. [Checking while you design](#8-checking-while-you-design)
9. [A worked example](#9-a-worked-example)
10. [Sources](#sources)

## 1. From the brief to a palette

Read the brief for:

- **The world and its materials**: wood, stone, iron, glass, flesh, cloth, water, energy. Each material has its own
  decay (section 4); a game whose interface is brass and oak can make its clicks of those materials.
- **The style**: realistic, stylized, retro, cute, dark. Retro means the chip voices; stylized often means fewer,
  cleaner layers; realistic means more layers, recorded sources and a shared space.
- **Scale**: big things sound lower and longer, small things higher and shorter.
- **How often each sound plays**: a frequent sound stays simple and understated; a rare, important one (a level won)
  may be richer and longer (Material Design 2, "Applying sound to UI").
- **What must be noticed**: warnings and threats use the attention cues of section 5.

Write the palette into the project before the sounds:

- `style.space`: one room for every sound that has none of its own. One size and decay put the sounds in one world;
  set `"style": false` on sounds that must stay dry (interface sounds usually do).
- `style.tilt_db`: a brighter (positive) or darker (negative) balance for the whole game.
- A shared key or base pitch for musical sounds, and a shared vocabulary of materials, so the set sounds like one
  game. Hero sounds stay consistent with each other (Material Design 2).

## 2. A recipe, an expanded recipe or layers

- **A recipe** is quickest: `python scripts/sfx.py recipes --name impact.wood_knock` lists its parameters, their
  ranges and what they do. Change them for the game: a heavier door is a lower `base_hz` and a louder `hollow_db`.
- **`sfx.py expand`** writes a recipe's layers and chain into the project at their current values; from there any
  value can change, layers can be added, sources swapped and chain stages added.
- **Layers from nothing** for what no recipe covers: a creature, a door, a vehicle, a mechanism, a motif of the game.

A recipe's defaults are this skill's own choices, tuned during development against measurements of CC0 game sound
libraries and listening feedback: a fair start, and the brief decides where it should go from there.

## 3. A sound as a stack of layers

Sound designers build a big hit from parts with separate jobs. Metcalfe's car explosion (*Sound On Sound*, October
2013) has five: a bass "sweetener" for weight, the immediate start of one explosion, the sustain and release of
another, metal tearing at the start to tell what is being destroyed, and debris raining down at the end; a distant
version would lose the close detail. Layering the attack, decay, sustain and release of different samples, each with
its own processing, keeps the parts clear of each other (Bazil, *Sound On Sound*, November 2012). The same stack serves
most sounds:

| Role | Carries | Sources here | Typical length (this skill's) |
|---|---|---|---|
| Transient | where the sound starts: its snap, click or crack | a `noise` burst through a band- or high-pass filter; a hard `modal` contact; a short `instrument` note | 2-20 ms |
| Body | what the thing is: its material, pitch and character | `modal` (material, shape, ratios); `osc` and `fm` tones; band-passed `noise` (flesh, cloth, air); `instrument` notes | 50-400 ms |
| Low | weight | a `sine` falling in pitch (a thump, a boom); `brown` noise through a low-pass (rumble) | 100 ms-1.5 s |
| Tail | distance and space | the `room` (or the project's space); a `delay`; a longer ring | 100 ms-2 s |

Glue and density: `saturation` adds harmonics (a low layer's harmonics reach speakers too small for its fundamental)
and thickens the sound. A `compressor` acts after the sound has begun: with a slow attack (5-20 ms) it lets the
transient through and brings the body down behind it, which sharpens the sound. It holds a transient down only when
its attack is clearly shorter than the transient, so on a click of 1-2 ms even a 0.8 ms attack can leave the peak
standing and make the sound peakier; `drive` or `saturation`, which flatten the peak itself, make such a sound denser.
In this skill's measurements an 8 ms attack on a punch raised its peak-to-loudness ratio by about 6 dB: check the
effect on `plr_db` in `check.json`.

Punch, for impacts: a short period of significant change in power, tied to the energy at that moment and to the
loudness around it (Fenton, Lee and Wakefield, AES 138th Convention, 2015). A strong transient with a body right
under it, not a click followed by silence.

**Events in time.** Many sounds are a short sequence, not one moment: a heavy gate groans on its hinge as it swings,
strikes, its latch drops and its bars rattle; a door creaks open and knocks shut. Give each event its own layers
starting at its own time (`start`), each built as a stack, and keep the lesser events well under the main one (in the
worked example the gate's creak sits about 12 dB under its strike). A sound that should be "creak, then bang" but is
only the bang, or only a soft thump, is the sequence missing, not a level to fix.

**Heavy, not pitched.** A few strong partials ringing for a long time are what a bell is, and vibrato is what an
instrument does. A heavy metal object needs neither: its strike is dense and soon damped (recorded hits, noise, many
detuned modes with a short t_d) and its weight is unpitched (a low thump falling in pitch, low noise, a rumble). In the
worked example the first gates, with a long steel ring, ringing higher modes, pitched rattles and a squeal with
vibrato, were heard as a bell or an instrument rather than a heavy gate.

**Water.** The `drops` source makes bubbles, and bubbles are heard one by one up to about ten thousand a second
(dripping, light rain), blending only towards a hundred thousand (heavy rain; van den Doel 2004). Surf foam, spray and
the hiss of a breaking wave are a blended texture: make them with noise (a high-passed hiss that swells after the
break and dies away within a second or two), not with a sparse population of bubbles, which sounds like drops
falling on something.

Narrow bands of noise carry little power: a pink noise band-passed at 250 Hz (q 1) sits about 17 dB under a
full-scale sine of the same length (this skill's measurement), so a body of noise often needs +10 to +20 dB of
`gain_db`. Mute layers one at a time (`gain_db` -80) to hear what each adds.

## 4. Material, size and force

- **Material** is heard mostly in the decay (Klatzky, Pai and Krotkov 2000): each mode of a `modal` source falls to
  1/e in t_d / f seconds; the study's estimates of where half its listeners would choose each material are t_d 3 for
  rubber and wood, 126 for glass and 447 for steel (it tested 3 to 300, so steel's lies beyond them). A metal object that is held or loaded rings for a fraction of that (`impact.metal_hit` uses 12) and carries its
  metal in a faint, long, higher ring; a hollow wooden box adds lower modes ringing a little longer than the board.
- **Size**: the modes of a bar fall with the square of its length (Russell, "Flexural Vibration of a Rectangular
  Bar"): halving the pitch is a bar about 1.4 times longer. Bigger things also take longer to speak and to die away.
- **Force and hardness**: hardness is heard in how long the contact lasts (van den Doel, Kry and Pai 2001): `hardness`
  shortens the contact pulse, so a hard strike reaches higher modes (the pulse's -3 dB point moves up from 300 Hz:
  this skill's model). A harder blow is louder and brighter, not only louder. Above 0.7 the strike also
  chatters for up to 15 ms, the micro collisions of a very hard contact (the same paper).
- **Shape**: `bar` for sticks, beams and mallet-instrument bars; `membrane` for skins and drums (the Bessel zeros of an
  ideal circular membrane); `ratios` for anything else: a plate, a bell, a pipe, or an object of the game's own.

## 5. Meaning and motion

- Upward motion for starting, opening and positive results; downward for ending and closing (Material Design 2,
  "Sound attributes").
- Irregular harmonics, a wide pitch range and rapid onsets draw attention (Brewster, Wright and Edwards, citing
  Edworthy, Loxley and Dennis 1991): keep them for what must be noticed.
- Roughness alarms. Screams concentrate their level's fluctuation at about 30-150 Hz, a range speech leaves alone;
  alarms use it too, listeners rate sounds as more frightening and react faster the more of it they hold, and it
  reaches the brain's fast appraisal of danger (Arnal et al. 2015). Trains of clicks were most aversive at 40-80 Hz and
  above about 130 Hz fused into a pitch (Arnal et al. 2019). `"tremolo": {"rate_hz": 60, "depth": 0.7}` puts a flutter
  in that range on a layer, this skill's way of using those findings for warnings, curses, creatures and dread. Keep it
  off anything friendly.
- Harsh and tense. Noise added to music made it feel less pleasant and raised arousal, and abrupt pitch shifts moved
  them too, downward ones lowering pleasantness and upward ones raising arousal (Blumstein, Bryant and Kaye 2012, music
  alone; with a video only the fall in pleasantness remained). Horror soundtracks held more non-musical sidebands and
  noisy female screams than chance would give them (Blumstein, Davitian and Kaye 2010).
- Earcons (Brewster, Wright and Edwards): pitched content between 150 Hz and 5 kHz; a note of a one- or two-note sound
  at least 0.03 s, of a longer figure at least 0.0825 s; the first note a little louder and the last longer, so the
  figure sounds complete. The interface recipes and references/ui-sounds.md carry these.
- A family of sounds that share a material, a key or a gesture reads as one system: the confirm and the back of one
  menu are mirror images (up and down) of one figure.

## 6. Recorded layers

Recordings bring what synthesis finds hard: the attack, body and texture of real objects and instruments. Two kinds
of recorded layer can be stacked with synthesis. FSD50K's CC0 recordings and the allowed banks put no condition on
the sound made with them; a pack someone imports may (see below).

**Field recordings (`sample` layers).** The main source is FSD50K (Fonseca et al. 2022): Freesound recordings
labelled with 200 classes of everyday sound (doors, engines, footsteps, water, wind, tools, animals, impacts, crowds,
machines and more), of which only those their authors dedicated to the public domain (CC0) are used. With the user's
agreement to a 2.9 GB download (4.3 GB once imported, about 7.2 GB until the downloaded archives are removed), `sfx.py samples --library LIB --download-fsd50k DATA` fetches this
project's release packs: 6,718 screened CC0 recordings in 190 of the classes, each checked against its SHA-256. With
`--source zenodo` it fetches a whole set from the dataset's Zenodo record instead (its robots.txt allows this; MD5-checked,
resumable): the evaluation set's 4,914 CC0 recordings (6.3 GB) or, with `--set dev`, 14,959 more (18.4 GB). Each recording
is measured and screened by this skill's own
thresholds: clipping, a lossy original (at 44.1 kHz or more, no energy above 16 kHz), a low peak, a short range between body and noise
floor (not asked of steady sounds such as rain, wind, surf, fire, engines and crowds, where the whole recording is the
sound), and whether its label is the predominant sound (FSD50K's ratings). A recording whose description asks for
credit, permission or notice, or speaks of commercial use, is flagged as well ('author note'): CC0 puts no condition on
it, but read the note (`--find` prints it) and respect the author's wish; many such notes only say that no credit is
needed. `samples --labels` lists the classes they are labelled with, and `--find "metal door" --passing` lists the recordings that pass every screen, the cleanest first, with their titles,
labels, peak and range; it matches whole words, `--not WORD`, `--min-seconds` and `--max-seconds` narrow it, and without
`--passing` a recording that fails is listed with its reasons. Recordings that FSD50K labels as speech, singing or a voice that may hold words, and those whose title or
tags name adult content, are screened out; people's sounds without words stay (laughter, screams, breathing, coughs), so a bed that must hold
nobody takes `--not voice`. Sounds the person already has (their own recordings, a library they downloaded or bought, the game's existing
sounds) go in with `sfx.py samples --add FOLDER_OR_ZIP`. Which licence holds is the person's to say: without `--licence`
(own, CC0-1.0, CC-BY-4.0, ...) the command imports nothing and prints the licence text that came with the sounds, to show
them. When the files name another licence or a restriction, or cannot be read, it stops and shows the lines: pass
`--confirm-licence` if the person holds to one licence for every file, or import each licence's files as their own
pack. A licence asking for credit needs `--author` (the creator's name, usually in the licence file) each time sounds go
into a pack. Never fetch
sounds from a site yourself unless its terms and robots.txt allow it; ask the person to
download them. Render with `--samples LIB` (or let the default library serve); for packs kept in separate libraries,
name each one: `--samples A --samples B`.

You cannot hear a recording, so read it before you use it. `sfx.py samples --events fsd50k-eval/54269` lists its strikes
(onset, peak, end and level) and prints a take for each, ready to paste into a layer's `file`; the takes of one layer
come at different levels, since a recording arrives at the level of its loudest moment, so give each its own `gain_db`.
`sfx.py samples --library LIB --map fsd50k-eval/54269`
prints its level, peak, spectral centroid, share of energy under 150 Hz and a pitch estimate frame by frame, its
quietest stretch and that stretch's octave bands (where its noise lies). Write `offset` from the map (just before the
event's onset), and place synthesized layers on events by where they land: a source event at time e plays at
`start + (e - offset) / 2^(cents / 1200)`.

Cleaning a recording. A field recording carries the place it was made in: wind, traffic, room tone.

- Where the sound has nothing, filter: a hinge's squeal with nothing under 1 kHz takes a high-pass at 1 kHz that
  removes the noise under it; the cut-off can move (a curve) and open for the strike that follows.
- Between events, close the envelope: noise in a gap is heard alone, so let the gaps and the end of a ring fall to
  silence.
- Steady noise everywhere: `"denoise": {"noise": [start, end]}` on the source subtracts the noise spectrum of a stretch
  that holds only noise (spectral subtraction, Boll 1979, with the over-subtraction and floor of Berouti, Schwartz and
  Makhoul 1979 as Lu and Loizou 2008 describe them); the map's quietest stretch is the usual choice. `reduce_db` (20) is how far under its level the noise is left and
  `over` (3) how hard it is subtracted (this skill's defaults: about 17 dB off steady white noise, a tone 34 dB above
  it unchanged). Keep both moderate: what subtraction leaves when pushed hard is a watery, tonal residue.

Changing what a recording says:

- Size: lower `cents` plays it slower and lower, a larger object (section 4: bigger things are lower and take longer);
  `cents` reaches four octaves either way, so a creak three octaves down becomes a vast groan.
- Time without pitch: `time_stretch` lengthens a recording and keeps its pitch, by the phase vocoder with
  phase locking (Laroche and Dolson 1999, as Driedger and Müller 2016 review it) for sustains, rings and swings, or by
  random-phase smearing (Nasca's Paulstretch, as the Csound manual describes it) for drones and textures from a creak,
  a hum or wind. A phase vocoder smears transients (Driedger and Müller 2016), so keep the strike out of it: `keep_attack_ms` plays the strike at its own speed and stretches only what follows, or give the strike its own layer and start the stretched layer just after it.
- Weight: a light recording takes synthesized low layers under its strikes (a falling sine thump, low noise for mass,
  a rumble), placed on its onsets.
- Distance: over distance air takes more of the high frequencies than the low, and the direct sound falls against
  the room's reflections; listeners read both as farther (Kolarik et al. 2016, a review): band-limit the recording, add
  an echo and much more room.
- Timing: a bouncing object strikes again and again at shrinking intervals, softer each time. In Warren and Verbrugge's
  study of glass (1984, as Giordano 2003 summarises it), listeners told bouncing from breaking almost without error
  from the timing alone: four damped pulse trains sharing one damping were a bounce, the same trains with different
  dampings a break. Repeat the strike with shrinking gaps and falling gains.
- A loop from a recording: a recording cannot be a loop's bed, but two takes of it can. Each is half the loop plus a
  crossfade; the second starts at the middle and runs past the end, and a layer that passes a loop's end wraps round to
  its start. Fade both with an equal-power curve (`env` points along a sine and a cosine), since the two stretches are
  unrelated, and take them from different parts of the recording. Two such pairs panned left and right make it wide.

How professionals use library sounds: as layers, not as finished sounds. A stock recording used as it comes sounds
like every other game that used it, so:

- Layer it with synthesis: a recorded door with a synthesized hollow body under it is a heavier door; a recorded
  impact as the transient over a synthesized boom; a footstep with a short low thump under it for an armoured guard.
- Give each variant its own take: `"file": ["119833", "154780"]` plays a different recording in each variant, the way
  game audio plays several takes so no two repeats are identical, and a take can start at its own time, so one
  recording of someone walking gives several steps: `"file": [{"file": "118985", "offset": 3.30}, {"file": "118985",
  "offset": 4.49}]`; `vary` adds small pitch and level moves on top (keep `pitch_cents` small when synthesized layers
  sit on the recording's events: pitch moves a recording's events in time too).
- Shape it: `offset` starts later in the recording (past a click, into the body), `reverse` turns a decay into a swell (it comes
  first, so `offset` then counts from the recording's end),
  `cents` moves it in pitch (higher is also shorter), the filter and envelope cut what the game does not need.
- The project's `style.space` puts recordings and synthesis in the same room.

The render records every recording it played (pack, file, licence, page, SHA-256, and for FSD50K its author, title
and Freesound page, the page standing in for an author name that is an email address) in `resolved.json`, on the audition page and in each export's manifest. CC0, public-domain and
the person's own recordings need no credit, and the record is there if the user wants to give one; a recording under
another licence passes its conditions to the sound, and the record gives the credit it asks for.

**Instruments (`instrument` layers).** Notes played dry on a sample bank of music-production's configuration; render
with `--config`. `python scripts/sfx.py banks --config CONFIG --find marimba` lists the banks and their presets
(`bank:program name`); only banks whose licence puts no condition on the sound are usable (CC0, the University of Iowa
recordings, the Unlicense, MIT). Uses:

- Musical interface sounds and jingles: mallet instruments (vibraphone, marimba, xylophone, glockenspiel or orchestra
  bells, tubular bells), harp, steel drum, chimes. A note or two under a synthesized sparkle gives it a body.
- Real attacks under synthesized bodies, or real bodies under synthesized transients: an orchestral bass drum or a
  timpani under a synthesized boom, a struck gong under a magic impact.
- Percussion kits for hits, pickups and rhythm-like feedback.

Both kinds come at a peak of 0.5 before the layer's gain; filter them, envelope them and pan them as any layer.
Instrument layers' variants move by `pitch_cents` (resampled) and `time_pct` (note times); sample layers' by
`pitch_cents` and their takes. The bank and pack names come from music-production's setup and the imported packs:
run `banks` and `samples` to see the ones installed.

## 7. Variants

A sound heard often needs variants so it does not repeat exactly: `vary` moves pitch (cents), times (%), filter
cut-offs (cents) and each layer's gain (dB) by up to its amount, differently for each variant, and the first variant
keeps the written values. Engines randomize too (pitch and volume randomizers in Wwise, FMOD and Godot); no controlled
study found gives amounts, so the recipes' amounts (often 20-40 cents of pitch and 5-8 % of time) are this skill's
choices. Keep a sound's identity: its pitch moving more than a few tenths of a semitone can read as a different
sound, and a melodic sound's variants should keep its intervals.

Two strikes of one pitch add or cancel by their phases, and those change with every variant: in one test a bell struck
three times at one pitch rang 12 dB louder in some variants than in others. Give repeated strikes slightly different
pitches.

## 8. Checking while you design

Render and read `check.json`. Each sound's `traits` (references/checks.md) give its peak-to-loudness ratio
(density), spectral centroid (brightness), flatness (noise against tone), length, attack, ring and the balance of its
bands: the numbers a listener's first impression rests on. Compare them with the brief, not with a norm: a heavy
door wants weight under 150 Hz and a slow decay; a crisp interface tick a short `active_s` and a quick `attack_ms`; a
bright centroid under a dark brief is the first thing to change. The measure tells you which layer to change (a peaky
sound wants more body or less transient; a bright one a lower body or a darker filter; a short one a longer body or
tail). Draw the pictures (`sfx.py spectrogram RENDER`) and look at them: you cannot hear, but you can see a click,
a strike that is not where it should be, a ring that goes on, weight that is missing. When a sound's main event must
lead (a strike after a swell, a slam after a creak), give its time as `peak_at`: the check then flags a layer that
comes out louder.

When the game will be played on phones, read `small_speaker_db` too: how much of the sound is left at 400 Hz and above,
roughly what a phone's speaker plays. An engine or a thump far below 0 there all but disappears on a phone; give it
harmonics (`saturation`, `drive`) or a layer an octave or two higher, so it still reads without its low end.

Measurements cannot hear. The person who listens decides: send the audition page, say what you changed and why, and
turn their notes into changes.

## 9. A worked example

This skill's `examples/` folder holds finished projects to read and copy from. `examples/hollow-road-recorded.json`
shows each of the techniques of section 6 on one sound, and `examples/tidewatch-keep-custom.json` designs the sounds of
a small, original coastal-fortress game from its brief:
a shared stone space with the interface kept dry, wood and iron impacts made from the recipes' layers and changed for
the game's materials, a stone block written as layers (no recipe makes stone), a marimba under the interface's
confirmation, an oak door and a keeper's footsteps from FSD50K's recordings stacked with synthesis (the steps of one
walking recording as takes), an iron gate from a recorded wrought-iron gate made larger, cleaned of its outdoor noise
and given synthesized weight on its measured strikes, water
magic from bubbles, a swept noise and a rising chord, and the sea on the rocks from noise alone. Its notes say why each
sound is built as it is.

## Sources

- Metcalfe, N. (2013). Designer Sounds. *Sound On Sound*, October 2013.
- Bazil, E. (2012). Layers Of Complexity. *Sound On Sound*, November 2012.
- Fenton, S., Lee, H. and Wakefield, J. (2015). Hybrid multiresolution analysis of 'punch' in musical signals. *AES
  138th Convention*, paper 9229.
- Klatzky, R. L., Pai, D. K. and Krotkov, E. P. (2000). Perception of material from contact sounds. *Presence* 9(4).
- Russell, D. A. Flexural vibration of a rectangular bar. Penn State, Acoustics and Vibration Animations.
- Abramowitz, M. and Stegun, I. A. (1964). *Handbook of Mathematical Functions*, Table 9.5.
- Brewster, S. A., Wright, P. C. and Edwards, A. D. N. *Guidelines for the Creation of Earcons*. University of Glasgow.
- Material Design 2: Applying sound to UI; Sound attributes.
- van den Doel, K. (2004). Physically-based models for liquid sounds. *Proceedings of ICAD 04*.
- Giannoulis, D., Massberg, M. and Reiss, J. D. (2012). Digital dynamic range compressor design: a tutorial and
  analysis. *Journal of the Audio Engineering Society* 60(6).
- Fonseca, E., Favory, X., Pons, J., Font, F. and Serra, X. (2022). FSD50K: an open dataset of human-labeled sound
  events. *IEEE/ACM Transactions on Audio, Speech, and Language Processing* 30, 829-852.
- Boll, S. F. (1979). Suppression of acoustic noise in speech using spectral subtraction. *IEEE Transactions on
  Acoustics, Speech, and Signal Processing* 27(2).
- van den Doel, K., Kry, P. G. and Pai, D. K. (2001). FoleyAutomatic: physically-based sound effects for interactive
  simulation and animation. *Proceedings of ACM SIGGRAPH 2001*.
- Arnal, L. H., Flinker, A., Kleinschmidt, A., Giraud, A.-L. and Poeppel, D. (2015). Human screams occupy a privileged
  niche in the communication soundscape. *Current Biology* 25(15), 2051-2056.
- Arnal, L. H., Kleinschmidt, A., Spinelli, L., Giraud, A.-L. and Mégevand, P. (2019). The rough sound of salience enhances
  aversion through neural synchronisation. *Nature Communications* 10, 3671.
- Blumstein, D. T., Davitian, R. and Kaye, P. D. (2010). Do film soundtracks contain nonlinear analogues to influence
  emotion? *Biology Letters* 6(6), 751-754.
- Blumstein, D. T., Bryant, G. A. and Kaye, P. (2012). The sound of arousal in music is context-dependent. *Biology
  Letters* 8(5), 744-747.
- Lu, Y. and Loizou, P. C. (2008). A geometric approach to spectral subtraction. *Speech Communication* 50, 453-466
  (on the over-subtraction and floor of Berouti, Schwartz and Makhoul, ICASSP 1979).
- Kolarik, A. J., Moore, B. C. J., Zahorik, P., Cirstea, S. and Pardhan, S. (2016). Auditory distance perception in
  humans: a review of cues, development, neuronal bases, and effects of sensory loss. *Attention, Perception, &
  Psychophysics* 78(2), 373-395.
- Driedger, J. and Müller, M. (2016). A review of time-scale modification of music signals. *Applied Sciences* 6(2),
  57 (which reviews Laroche and Dolson's phase locking of 1999).
- Csound Reference Manual: `paulstretch` (Nasca's Paulstretch method).
- Giordano, B. L. (2003). Everyday listening: an annotated bibliography. In Rocchesso, D. and Fontana, F. (eds.), *The
  Sounding Object* (on Warren and Verbrugge's study of breaking and bouncing, 1984).
