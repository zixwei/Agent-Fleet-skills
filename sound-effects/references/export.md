# Exporting for an engine (`sfx.py export`)

`python scripts/sfx.py export render_3 --target godot --output export_godot` writes a render's masters as the files an
engine takes, into a new folder:

- `sfx/`: the files, named as in the render (`<sound>_<nn>.<ext>`), 48 kHz, the render's channels. WAV and FLAC are
  16-bit with triangular dither; `generic` also keeps the 24-bit masters (`sfx/24bit/`, beside `sfx/16bit/`).
- `manifest.json`: per sound (`sounds[].files`) every file with its sound, variant, use, format, frames and loop, and its measurements after
  export (true peak, loudness, for a loop how its end meets its start, for an encoded file how far its sound moved and
  how many frames decoding added); per sound the settings suggested for the engine, with the documents they come from;
  and, when sample layers played library recordings, `recordings`: each recording's pack, file, author, licence,
  download page and SHA-256 (CC0, public-domain and the user's own recordings need no credit, and the list is there
  for the record or an optional credit; a recording under another licence is named with what it asks). Each file of a
  sound lists the `recordings` it plays, because variants can play different takes.
- `files.json`: every file with its size and SHA-256 (what `clean` goes by).

`--formats wav,flac,ogg,mp3` adds formats (WAV is the default for every target). FLAC, Ogg Vorbis and MP3 are encoded by
FFmpeg (`--ffmpeg`, `SFX_SKILL_FFMPEG`, music-production's configuration with `--config`, or the PATH), from the 24-bit masters for the lossy formats (Vorbis
quality 5, MP3 VBR quality 2: this skill's choices) and from the 16-bit file for FLAC, then decoded back and measured.
Homebrew's `ffmpeg` has no Vorbis encoder (its `ffmpeg-full` does): `doctor` says so, and an export asking for Ogg stops
before writing anything.

| Target | What it writes | Why (source) |
|---|---|---|
| `godot` | WAV; for each one-shot with variants an AudioStreamRandomizer, `<sound>.tres` (random without repeats, `random_pitch` 1, `random_volume_offset_db` 0); `--res-path` (default `res://sfx`) is the project folder the files go in | Godot recommends WAV for short, repeated sound effects and Ogg Vorbis for long sounds, and follows a WAV file's own loop by default (Godot docs, *Importing audio samples*; *AudioStreamRandomizer*) |
| `unity` | WAV; suggested import settings per sound: PCM and Decompress On Load for short sounds, ADPCM for noisy hits played in numbers (impacts and foley up to 2 s), Vorbis for longer ones, Streaming for stereo loops from 15 s and Compressed In Memory for shorter or mono ones, which several sources can share (the 2 s and 15 s lines are this skill's; settings read from the Unity 6.6 manual, and an export imported into Unity 6000.5 kept every clip's channels, rate and length); an Audio Random Container for variants | Unity 6.6 Manual, *Audio Clip Import Settings reference*, *Audio playlist randomization*; no `.meta` files, which Unity makes itself |
| `unreal` | 16-bit WAV; loops through the Looping flag of the Sound Wave or Wave Player; variants through a Sound Cue Random node with Randomize Without Replacement | Unreal converts every import to 16 bits and does not dither 24-bit files, so it recommends 16-bit files (UE 5.9, *Importing Audio Files*; *Sound Cue Reference*) |
| `roblox` | WAV (FLAC, Ogg or MP3 on request); counts the uploads, one per variant | files under 20 MB, 7 minutes and 48 kHz; 100 assets per 30 days for an unverified account, 2000 when ID verified (Roblox, *Audio assets*) |
| `web` | WAV (Ogg and MP3 on request) | play with `decodeAudioData` and an `AudioBufferSourceNode`, `loop = true` for loops; Safari plays Ogg Vorbis only from 18.4 (WebKit's release notes), and MDN gives a codec latency of at least 100 ms for Vorbis and MP3 (MDN, *Web audio codec guide*) |
| `generic` | 16-bit and 24-bit WAV | nothing engine-specific |

## Where to work

A render holds hundreds of files. Keep render and export folders out of what the game ships. Each render carries a
`.gdignore`, so a Godot project skips it; in Unity keep them outside `Assets`. Copy the export's `sfx/` where the game
wants it. In Godot, a one-shot's variants come with a `<sound>.tres`; a loop is one file, `<sound>_01.wav` (a long loop can go as Ogg
to keep it small: `--formats ogg`, then switch Loop on in the Import dock).

## Playing on the web

- Make the audio context at the files' rate: `new AudioContext({sampleRate: 48000})`. In Chromium 156 a one-second 48 kHz
  loop decoded by a 44.1 kHz context came out a frame short and jumped at its join by twice the largest step inside it;
  at 48 kHz it joined exactly.
- A page's context starts suspended until the first tap or key: call `resume()` in that handler, and suspend it when the
  tab is hidden, or a loop plays on. A page opened from disk (`file://`) cannot `fetch` its sounds: serve it, even with
  `python -m http.server`.
- Leave a `DynamicsCompressorNode` off the master bus. Measured in Chromium 156 on a steady tone, it delays everything by
  about 6 ms and lets the sound in about 10 dB low, recovering over some 100 ms, so the first press after a page opens
  comes out quiet. The set is already levelled under a ceiling; if many sounds can pile up, use a soft-clipping
  `WaveShaperNode`.
- WAV keeps exact timing, and a set of short sounds stays small. Where size matters, use MP3 for long one-shots (Safari
  before 18.4 has no Vorbis) and keep loops as WAV, short enough to stay small, since MP3 pads its ends and will not join.

## Loops

Masters carry a `smpl` loop over the whole file, its end stored as the last frame played (the WAV convention). Godot
4.7 reads that field as its `loop_end` and plays frames `loop_begin + 1` to `loop_end` before returning
(`scene/resources/audio_stream_wav.cpp`), so the `godot` target writes each loop with its first frame repeated after
its end and `loop_end` = the period: every pass then plays the period exactly once. Ogg and MP3 carry no loop points:
switch Loop on in the engine. Other engines loop the whole file when told to (Unity `AudioSource.loop`, Unreal Looping,
Roblox `Sound.Looped`, Web Audio `loop`).

## Flags

| Flag | When |
|---|---|
| `true_peak` | an exported file's true peak lies more than 0.1 dB over the render's ceiling (lossy codecs add peaks) |
| `loop_length` | a loop decodes to a different length: it will not join (MP3 pads; use WAV or Ogg) |
| `seam` | a loop jumps where its end meets its start after encoding |
| `time_shift` | an encoded file's sound starts 1 ms or more after the master's: it lags its trigger |
| `roblox_size`, `roblox_length`, `roblox_allowance` | Roblox's limits above |
| `web_ogg` | Ogg for the web: Safari plays Vorbis only from 18.4 (macOS 15.4, iOS 18.4) |

Measured with the FFmpeg of music-production 0.8.0's configuration: MP3 files decoded to their master's length (FFmpeg
honours the encoder's gapless information; other decoders, browsers among them, may not), and Ogg one-shots of a
fraction of a second came out up to one long Vorbis block (2048 frames) longer or shorter at their end, with their start
in place. Each file's own numbers are in the manifest.
