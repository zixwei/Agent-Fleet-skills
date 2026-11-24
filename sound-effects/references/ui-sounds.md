# Interface sounds

The `ui.*` recipes and the rules they encode. Each rule cites its source; the parameter values (frequencies, times,
levels) are this skill's own sound design and are judged by ear on the audition page.

## Rules

| Rule | Recipes | Source |
|---|---|---|
| Pitched content between about 150 Hz and 5 kHz, where it is not easily masked and most listeners hear it | all | Brewster, Wright and Edwards |
| A sound of one or two notes keeps each note at 0.03 s or longer; notes of a longer figure at 0.0825 s or longer (for interface sounds: a chip arpeggio runs faster on purpose) | all; `ui.success` | Brewster, Wright and Edwards |
| The first note slightly louder and the last note longer, so the figure sounds complete | `confirm`, `back`, `notify`, `success` | Brewster, Wright and Edwards |
| Upward motion for starting, opening and positive results; downward for ending and closing; repetition for waiting | `confirm`, `toggle_on`, `open`, `success`; `back`, `toggle_off`, `close` | Material Design 2, Sound attributes |
| A frequent primary sound stays simple and understated, fit to be heard often | `tick`, `hover` | Material Design 2, Applying sound to UI |
| A low-priority sound uses a softer, quieter timbre; important notifications a brighter one, which carries in busy places | `tick`, `hover`; `notify` | Material Design 2, Sound attributes |
| A softer attack sounds more ambient, a sharper one more energetic | `hover` | Material Design 2, Sound attributes |
| Tonal sound for personality, emotion and state changes; atonal sound for motion and a feel of touch | `select`; `toggle_*`, `open`, `close` | Material Design 2, Sound attributes |
| Irregular harmonics and a distinct rhythm draw attention without loudness | `error` | Brewster, Wright and Edwards, citing Edworthy, Loxley and Dennis (1991) |
| A level that flutters at about 30 to 150 Hz is rough, the quality that makes screams and alarms alarming; repeated clicks were most aversive at 40 to 80 Hz | `error` (its flutter is set in that range: this skill's choice) | Arnal et al. (2015, 2019) |
| Notifications are shorter than hero sounds and fit for repetition; hero sounds celebrate a significant positive action, occur rarely and stay consistent | `notify`; `success` | Material Design 2, Applying sound to UI |
| Reverb and delay used sparingly | all (`success` has a short echo) | Material Design 2, Sound attributes; Godot's audio import guide leaves space to the engine's buses |
| A UI sound starts at its first sample: audio feedback has a total budget of about 20-70 ms, and the device takes part of it | all (checked by `check`) | Kaaresoja (2016) |

## The recipes

| Recipe | For | Default trim |
|---|---|---|
| `ui.tick` | stepping through a list, scrolling: a very short tick | -6 dB |
| `ui.hover` | the pointer moving onto an item: the softest cue | -9 dB |
| `ui.select` | choosing an item: one clear note | -3 dB |
| `ui.confirm` | a positive confirmation: two notes rising | 0 dB |
| `ui.back` | going back or cancelling: two notes falling | -1 dB |
| `ui.error` | refused or not possible: two low, rough pulses | 0 dB |
| `ui.toggle_on`, `ui.toggle_off` | a switch: a quick slide up or down | -3 dB |
| `ui.open`, `ui.close` | a panel or menu: an airy rise, a falling sweep | -4 dB |
| `ui.notify` | a notification: two bright, bell-like notes | 0 dB |
| `ui.success` | completion or a reward: a short rising arpeggio of held notes over a warm chord that rings out | 0 dB |

`python scripts/sfx.py recipes --category ui` lists every parameter with its range and how it varies;
`--name ui.confirm` shows one recipe with its rules. The trims are this skill's starting balance (quieter for frequent,
low-priority sounds); the developer's listening sets the final balance with the audition page's level sliders.

Variation amounts (for example ±30 cents on a base pitch, ±6 % on lengths) are this skill's defaults: no published
threshold for how much game sounds should vary was found, so they are kept small enough for an interface sound to stay
recognisable, and a project can change them per sound.

## Sources

- Brewster, S. A., Wright, P. C. and Edwards, A. D. N. *Guidelines for the Creation of Earcons*. University of Glasgow,
  Department of Computing Science (web page, updated guidelines after their 1992-1995 experiments).
- Edworthy, J., Loxley, S. and Dennis, I. (1991). Improving auditory warning design: relationships between warning
  sound parameters and perceived urgency. *Human Factors* 33(2), 205-231 (as cited by Brewster et al.).
- Arnal, L. H., Flinker, A., Kleinschmidt, A., Giraud, A.-L. and Poeppel, D. (2015). Human screams occupy a privileged
  niche in the communication soundscape. *Current Biology* 25(15), 2051-2056.
- Arnal, L. H., Kleinschmidt, A., Spinelli, L., Giraud, A.-L. and Mégevand, P. (2019). The rough sound of salience enhances
  aversion through neural synchronisation. *Nature Communications* 10, 3671.
- Google. Material Design 2: *Applying sound to UI* and *Sound attributes* (m2.material.io).
- Kaaresoja, T. (2016). *Latency guidelines for touchscreen virtual button feedback*. PhD thesis, University of
  Glasgow.
- Godot Engine documentation: *Importing audio samples* (best practices).
