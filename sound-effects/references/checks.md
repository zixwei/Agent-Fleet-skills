# What `render` and `check` measure

Every master file is measured as it is on disk. A flag is a question for whoever revises the sound; no measurement
here says whether a sound is good, and the developer's listening decides that. No flags does not mean clean either: a
noise inside a recording or an instrument's sample can look like part of the sound, so look at the spectrogram of
anything with a long tail.

## Per file (check.json, `files`)

| Field | What it is |
|---|---|
| `duration_s` | length |
| `sample_peak_db`, `true_peak_db` | highest sample; true peak as ITU-R BS.1770 defines it (four times oversampled) |
| `brief_lufs` | one-shots: the loudness of the loudest 200 ms (BS.1770 K-weighting, windows starting at every sample), what the level step matches them by. Under about 200 ms perceived loudness follows a sound's total energy, and a sound held longer gets no louder (Scharf 1978, reviewed by Oceák, Winkler and Sussman 2006), so a 100 ms sound reads 3 dB under the same sound held. The 200 ms window is this skill's reading of that figure; BS.1770 does not define it |
| `max_momentary_lufs` | the highest momentary loudness (BS.1770 K-weighting, 400 ms windows as EBU Tech 3341 defines them), over windows starting at every sample. A sound shorter than 400 ms is read as one window with silence after it, so a 100 ms sound reads 6 dB under the same sound held for 400 ms |
| `integrated_lufs` | integrated loudness (BS.1770-4 gating); a one-shot is levelled by `brief_lufs` and a loop by this, so when a loop has long gaps compare its `max_momentary_lufs` with the one-shots': its loudest moments can reach their level although its integrated level sits well under it |
| `onset_ms` | one-shots: time from the first sample to the first sample within 40 dB of the peak (a recording's noise floor can set it) |
| `strike_ms`, `strike_rise_ms` | one-shots: when the sound lands, the first millisecond within 12 dB of the loudest one; and from there to the loudest (short for a strike, long for a swell) |
| `ending` | one-shots: the last millisecond's level against the peak, the last 10 ms RMS against the peak, and `cut` |
| `dc_offset`, `clipped` | the largest channel mean; samples at or beyond 0.999 of full scale |
| `bands`, `centroid_hz` | shares of energy below 150 Hz, between and above 5 kHz; spectral centroid over 20 Hz-20 kHz. Both come from the sound's energy spectrum with no window, so every moment counts by its energy (a window over a whole one-shot would all but hide its onset) |
| `stereo` | for two channels: correlation, and the level change of the mono sum |

A loop is measured as it plays on, its end running into its start: true peak with the oversampling filter run round
the join, and loudness from the period repeated, so windows across the join count like any other. It adds:

| Field | What it is |
|---|---|
| `integrated_lufs` | integrated loudness (BS.1770-4 gating: -70 LUFS absolute, -10 LU relative) |
| `loudness_range_lu` | loudness range by EBU Tech 3342: short-term loudness (3 s windows, every 100 ms), gated at -70 LUFS and 20 LU under the power mean, the 95th percentile less the 10th; for a short loop it rests on few independent windows |
| `halves_db` | integrated loudness of the second half less the first: a rise or fall inside the loop that repeats each time round |
| `seam` | `step`: the jump from the last frame to the first; `largest_inner_step`: the largest jump between neighbouring frames inside; `join_level_db` and `quietest_inner_level_db`: the level of the 2 ms across the join and of the quietest 2 ms inside, against the median 2 ms level; `spectral_db` and `inner_spectral_db_p95`: the third-octave distance between the last and first 100 ms, and the 95th percentile of the distances between neighbouring 100 ms windows inside (for comparison only; no flag) |

Per sound (`sounds`): how far the variants' loudness spreads (integrated loudness for loops), how each variant
moved from the first (length, centroid, and the mean difference of third-octave levels, a measure of this skill, not a
perceptual scale), and `traits`: the sound's measures, the median of its variants (below; a loop has no start or end, so it leaves out `active_s`,
`attack_ms`, `decay20_ms` and `peak_s`).

## Traits

Each sound's `traits` describe what a listener notices first, so the agent, who cannot hear, can aim a change and
check its effect. They are compared with nothing and raise no flag (traits.py gives the definitions):

| Trait | What | Reading |
|---|---|---|
| `plr_db` | true peak less the loudness of the loudest 200 ms (`brief_lufs`), so that short and long sounds compare | high: peaky, little body, the sound levels quietly; low: dense, little left of its attack |
| `crest_db` | true peak less the RMS of the part within 40 dB of the peak | as `plr_db`, without the loudness weighting |
| `centroid_hz` | spectral centroid, the energy-weighted mean frequency, of that part (Peeters et al. 2011) | high: brighter, often thinner; low: darker |
| `flatness` | geometric over arithmetic mean of the energy spectrum, 100 Hz-16 kHz (Peeters et al. 2011) | near 1: noise-like; near 0: tonal |
| `small_speaker_db` | the share of the energy at 400 Hz and above, in dB (400 Hz is this skill's stand-in for a phone's speaker, not a measured device) | near 0: it survives a phone; far below: most of it is low end a phone will not play |
| `active_s`, `attack_ms`, `decay20_ms`, `peak_s` | from the first to the last millisecond within 40 dB of the peak; from its start to the peak; from the peak to the last millisecond within 20 dB of it (edges this skill's choice); the time of the loudest millisecond | how long it speaks, how fast it hits, how long it rings, where it hits hardest |
| `bands` | shares of the energy in six bands: `sub` 20-60 Hz, `low` 60-150, `low_mid` 150-500, `mid` 500-2000, `high_mid` 2-5 kHz, `high` 5-20 kHz (the file-level `bands` above are three other, wider bands) | where its weight lies |

`python scripts/sfx.py check RENDER --traits` prints them, one line per sound. Look as well as read: `python scripts/sfx.py spectrogram RENDER` draws each sound's picture (its level with the
loudest millisecond marked, and its spectrogram from 20 Hz to 20 kHz on a log scale) into RENDER/spectrograms; open
the PNG. A click is a thin vertical line, a strike a bright column fading right, a ring a horizontal line, noise a
smear, weight the bottom rows.

Read them while you design: a punch whose `attack_ms` is long has no snap; a door whose `bands` hold almost nothing
under 150 Hz has no weight; a bright `centroid_hz` under a dark brief is the first thing to change.

## Flags and their thresholds

| Flag | When | Why |
|---|---|---|
| `click` | a one-shot holds an isolated click: a millisecond whose energy above 3 kHz rises more than 18 dB over the 10 ms before it and falls back within milliseconds, away from the sound's loudest moment, in a sound that does not crackle on purpose (`crackle`) | a click is a short impulsive disturbance, told by how far it stands out from what surrounds it; a struck object rings on and is not taken for one, a cut into a waveform or a reversed transient ending abruptly is (thresholds this skill's choice) |
| `peak_at` | neither the first variant's loudest millisecond nor where it lands (`strike_ms`) lies within 20 ms of the sound's `peak_at` | the main event should lead; another layer (a swell, a reversed tail, a later hit) is louder (tolerance this skill's choice) |
| `onset` | a `ui` sound starts more than 1 ms into its file | audio feedback has a total budget of about 20-70 ms (Kaaresoja 2016) and the device takes part of it; leading silence also adds latency in engines (Godot's import guide) |
| `ending` | a one-shot's last millisecond still reaches -40 dB of the peak | a sound that stops at a level clicks |
| `seam` | a loop jumps at its join by more than twice the largest step inside it, or the 2 ms across its join are more than 3 dB quieter than any 2 ms inside it and 6 dB under its median | a jump clicks and a dip (a fade at the ends) is heard every time round; the engine's loops join by construction, so this points at a hand-made fault |
| `dc` | an offset beyond 0.001 (-60 dBFS) | wastes headroom and thumps when the sound starts and stops; every render already ends with a first-order high-pass at 10 Hz, so this points at something unusual |
| `clipped` | any sample at full scale | the level step keeps every file under its ceiling, so this means a fault |
| `true_peak` | more than 0.1 dB over the ceiling | the ceiling (default -1 dBTP, PlayStation's recommended maximum) must hold; 0.1 dB allows for dither |
| `band` | a `ui` sound with more than half its energy below 150 Hz or above 5 kHz | Brewster, Wright and Edwards give 150 Hz-5 kHz for earcons' pitches; applying it to energy is this skill's choice, so a high note with strong harmonics can raise it: look, then decide |
| `variant_level` | a sound's variants spread by more than 0.5 dB | after the level step they should sit at one loudness |
| `variant_timing` | a struck sound's variants (those that land in their first 150 ms and reach their loudest within 50 ms after) land more than 20 ms apart | played one after another, steps and hits fall unevenly; line them up with each take's `offset` |
| `loop_halves` | a loop's second half is more than 20 dB louder or quieter than its first, or one half holds next to nothing | a swell or a gap heard every time round, often a whole-loop layer with a fading envelope |
| `buried_event` | an event of a loop (a layer of at most 3 s) stands less than 3 dB over the rest of the loop, over its loudest 50 ms in the octave around its own centre of energy | a crow under the wind will not be heard; each event's figure is `over_rest_db` in `resolved.json` |
| `loop_level` | a loop's integrated loudness is less than 3 dB under the loudest 200 ms of the set's loudest one-shot | the level step matches a loop by its integrated loudness and a one-shot by its loudest 200 ms, so an untrimmed bed reads as loud as the hits and sits on top of them. Give it a negative `trim_db`, or leave it if the loop is meant to lead (the loops of this skill's own test games sat 7 to 21 dB under) |
| `short_recording` | a sample layer's recording ends at least 10 ms before the layer does, still sounding: its envelope there, times the recording's last 10 ms against its level over the part played, is within 20 dB of full (a recording that has died away by its end leaves no hole) | the layer falls silent there: a hole in a loop, a sound cut short (the offset, pitch and stretch all count) |
| `stereo` | left and right correlate below -0.2 | the sound will cancel when summed to mono; independent channels (a spread of 1) wander around 0 by chance, a little either way |

The 1 ms, -40 dB, twice the step, 3 dB, 6 dB, 0.001, 0.5 dB, -0.2, "more than half", 12 dB, 20 ms, 50 ms and 150 ms
and the 3 dB of `buried_event` and `loop_level` and 20 dB of `loop_halves` are this skill's choices, set to catch faults rather than to grade sounds.

## The level step

`match` (the default) brings every file of the set to one loudness plus its sound's `trim_db`: the level asked for in
`level.target_lufs`, or without one the highest at which no file passes the true-peak ceiling by more than `limit_db`
(6 dB by default, so the peakiest file gives up all of it; a brief that wants its hits sharp or its set quiet takes less).
With a level asked for, a trim moves only its own sound; the level gives way, and `render` says so, when a file's peaks
cannot reach it within `limit_db`; a file that passes the ceiling goes
through a true-peak limiter (1.5 ms lookahead) that brings it back under, so a peaky sound gives up a few dB of its
highest peaks instead of holding the whole set down. The limiter holds its gain for 25 ms (a period of 40 Hz) after each peak: a gain that dipped at every tall peak of a
low, lopsided wave (an engine's driven pulse) and recovered in the troughs would take more off one polarity than the
other and leave an offset. A file whose limiting would still leave an offset near the `dc` flag is limited less.
Limiting a short sound lowers its loudness nearly as much as its peak, so the level step searches each limited file's
gain until it reaches the set's loudness; `check` and `render` report which file set the level and how far each file
was limited (`limited_db` in `resolved.json`, where each variant's `layers` also give every layer's peak and when it comes, before the chain: the place to look when one sound sets the level or a flag points at a moment). CC0 game sound libraries measured during development were 4 to 10
dB louder than this skill's sets before the limiter (this skill's measurement, not a target). A one-shot's loudness is that of its
loudest 200 ms (`brief_lufs`: the 400 ms of the momentary window reads a short sound too low, and matching by it pushes
clicks above the sounds they should sit under); a loop's is its integrated loudness, the measure BS.1770 gives for programmes (a loop is heard for
long stretches, and one gust should not set its level as a one-shot's peak does). A very peaky sound holds the whole
set down: `render` and `check` say which, how far a lower `trim_db` on it would let the rest of the set rise and which
file would set the level then (`level.next` in `resolved.json`); that, or a softer attack, frees the rest. `peak` puts each file's true peak at the ceiling
less any negative trim.

Platform loudness recommendations measure a whole game's output over at least 30 minutes of play (PlayStation:
-24 LUFS for consoles and -18 LUFS for portable devices, each within 2 LU, and at most -1 dBTP; Audiokinetic 2024), not
single sounds, so the skill sets no loudness target for a file: the game's mix sets each sound's final level.

## Sources

- ITU-R BS.1770 (algorithms to measure audio programme loudness and true-peak audio level); EBU Tech 3341 (loudness
  metering); EBU Tech 3342 (loudness range).
- Couthier, L. and Schembri, D. (2024). Mastering a Game with Wwise, Part 1. Audiokinetic blog.
- Taylor, G. (2012). Video Games and Loudness Standards, interview by M. Taylor. *Designing Sound*.
- Kaaresoja, T. (2016). *Latency guidelines for touchscreen virtual button feedback*. PhD thesis, University of
  Glasgow.
- Brewster, S. A., Wright, P. C. and Edwards, A. D. N. *Guidelines for the Creation of Earcons*. University of Glasgow.
- Godot Engine documentation: *Importing audio samples*.
- Oceák, A., Winkler, I. and Sussman, E. (2006). Loudness summation and the mismatch negativity event-related brain
  potential in humans. *Psychophysiology* 43(1), 13-20 (its review of loudness and duration, citing Scharf, B. (1978).
  Loudness. In *Handbook of Perception*, vol. 4, Academic Press, 187-242).
- Peeters, G., Giordano, B. L., Susini, P., Misdariis, N. and McAdams, S. (2011). The Timbre Toolbox: extracting
  audio descriptors from musical signals. *Journal of the Acoustical Society of America* 130(5), 2902-2916 (spectral
  centroid and flatness).
- Giannoulis, D., Massberg, M. and Reiss, J. D. (2012). Digital dynamic range compressor design: a tutorial and
  analysis. *Journal of the Audio Engineering Society* 60(6).
