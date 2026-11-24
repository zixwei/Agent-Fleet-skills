# Music model reference

What each AI music model expects. Checked 17 Nov 2026. Models change every few months: if the user's model or version is not here, say so and fall back to a model-neutral format.

## Suno (suno.com)

**Models:** v6 (Pro, Premier) · v6-wild (Pro, Premier; experimental, less predictable) · v6-mini (everyone, incl. free). v4.5, v5 and v5.5 were retired when v6 launched on 9 Sep 2026.

**Input:** Three boxes: Style, Lyrics, Exclude (plus a title). Section tags and performance cues go in the lyrics, in square brackets.

- Style 1,000 characters, Lyrics 5,000 (quality drops past about 3,000: sections get rushed), Exclude 1,000, Title 100. Text past a limit is cut silently.
- Tags: [Intro] [Verse] [Pre-Chorus] [Chorus] [Post-Chorus] [Bridge] [Outro] [Instrumental]; per-section direction inside the tag, e.g. [Verse | whispered, close mic]. Anything outside brackets is sung.
- Put the most important style words first; the start of the style box weighs most.
- Negatives go only in Exclude, as 'no X', two or three at most. Typed into the style box, 'male vocal' is read as an instruction to include one.
- Tell the user to set the Variety slider to 0 (otherwise Suno rewrites the style prompt) and to raise Style Influence above its 50% default when the prompt is precise.
- v6 can also edit an existing song in plain language (change one section, or one word of a lyric, keeping the rest), build mashups, and take audio, image and video references in one prompt; suggest these before regenerating from scratch.
- v6 for reliable results, v6-wild for experiments, v6-mini on free plans. Judge on several takes: two takes of one prompt can differ more than two prompts.
- Describe the sound instead of naming artists.

**Rights:** Free plan: no commercial use. Paid plans: commercial use; downloads are capped per plan since Sep 2026.

Sources: [Suno: Introducing v6](https://suno.com/blog/introducing-v6) · [Suno v6 FAQ (official)](https://help.suno.com/en/articles/13924481) · [Suno v6 prompt guide (Variety, Style Influence)](https://hookgenius.app/learn/suno-v6-guide/) · [Suno character limits](https://hookgenius.app/learn/suno-character-limits/)

## Udio (udio.com)

**Models:** v2 · v1.5 · v1.5 Allegro (faster, punchier, suits pop and electronic).

**Input:** A prompt (topic or free text plus genres and tags) and a lyrics box; Manual Mode takes exact tags only, with no automatic refinement. Songs are built clip by clip and extended section by section.

- Lyrics tags: [Intro] [Verse] [Pre-chorus] [Chorus] [Post-chorus] [Hook] [Refrain] [Bridge] [Drop] [Interlude] [Instrumental Break] [Outro].
- Be granular about genre ('shoegaze', not 'rock') and describe sound and era.
- Write lyrics for the section being generated or extended, not the whole song at once.

**Rights:** Downloads of audio, video and stems have been switched off on every plan since 29 Oct 2025 (after the Universal settlement); songs can only be played inside Udio. A licensed platform was announced for 2026. Do not recommend Udio when the user needs a file (YouTube, ads, games, release).

Sources: [Udio guidance tags](https://help.udio.com/en/articles/11166249-level-up-your-creations-with-guidance-tags) · [Udio: Prompt like a master](https://help.udio.com/en/articles/10716541-prompt-like-a-master) · [Udio v1.5](https://www.udio.com/blog/introducing-v1-5) · [What happened to Udio downloads](https://music.loop.fans/ai/what-happened-to-udio)

## Google Flow Music (Flow Music, Gemini (Lyria))

**Models:** Lyria 3.5 (Jul 2026, Flow Music and Gemini; up to about 3 min, variable length) · Lyria 3 Pro (up to 3 min) · Lyria 3 Clip (30 s) · Lyria RealTime (live, interactive).

**Input:** One natural-language prompt: genre and era, mood, instruments, tempo and groove, vocal style and language, then lyrics after 'Lyrics:'. Up to 10 reference images can set the mood.

- Lyria 3 Pro and 3.5 follow timestamps: [00:00] intro..., [00:15] verse..., [01:10] chorus... Lyria 3 Clip makes 30 seconds only.
- Vocal languages (Lyria 3 / 3 Pro): English, German, Spanish, French, Hindi, Japanese, Korean, Portuguese. Name the language ('sung in Japanese').
- There is no negative field: describe what you want. Write 'instrumental' to get no vocals.
- Ask for backing vocals in the prompt ('backing singers echo the last line').
- Requests for an artist's voice or copyrighted lyrics are blocked: describe era, instruments, texture and vocal register instead.

**Rights:** Commercial use on paid Flow Music plans. Every track carries a SynthID watermark (it marks AI origin, it does not prove clearance).

Sources: [Lyria (Google DeepMind)](https://deepmind.google/models/lyria/) · [Prompting guide for Lyria 3 Pro (Google Cloud)](https://cloud.google.com/blog/products/ai-machine-learning/ultimate-prompting-guide-for-lyria-3-pro) · [Lyria 3.5 review](https://www.therundown.ai/tools/lyria-3-5)

## ACE Studio (ACE Music, ACE-Step, ACE Studio app)

**Models:** ACE Music v1 (11 Nov 2026, web app; ranked first on a public vocal leaderboard at launch) · ACE Music v1.1 Preview (writes an editable score first) · ACE-Step 1.5 (open source, with StepFun) · the ACE Studio app's vocal synth.

**Input:** ACE Music: a text prompt, your own lyrics, a melody (MIDI file, on-screen keyboard or humming) or an image; two songs per generation. ACE-Step 1.5: a caption (tags or prose) + lyrics + separate settings for BPM (30-300), key, time signature and duration. ACE Studio app: MIDI notes + lyrics, one syllable per note.

- ACE-Step lyrics: [Intro] [Verse] [Pre-Chorus] [Chorus] [Bridge] [Outro] [Build] [Drop] [Breakdown] [Instrumental] [Guitar Solo]; a hint can ride on a tag, [Chorus - anthemic]; UPPERCASE words are sung harder; (parentheses) are backing vocals; blank line between sections.
- ACE-Step: lines of 6-10 syllables, matching lines within 1-2 syllables; up to 10 min (30 s to 4 min is most stable); 50+ languages. ACE Studio voices: 8 languages.
- ACE-Step: put BPM, key and length in their own settings, not only in the caption. For an instrumental, put only tags in the lyrics, or the words get sung.
- ACE-Step: don't stack many tags or conflicting styles; turn a conflict into a progression ('starts with soft strings, becomes heavy rock').
- ACE-Step Turbo is forgiving of vague prompts; SFT follows long, detailed descriptions better.
- ACE Music: use a melody input when the user already has a tune; it keeps it.

**Rights:** ACE Music free plan: no downloads, no commercial use; paid plans: downloads and commercial rights. No public API. ACE-Step 1.5 is open source and runs locally.

Sources: [ACE Studio](https://acestudio.ai/) · [ACE Music v1](https://creatune.ai/ace-music-v1) · [ACE-Step 1.5 tutorial](https://github.com/ace-step/ACE-Step-1.5/blob/main/docs/en/Tutorial.md)

## Mureka (mureka.ai)

**Models:** V9.5 (default) · V9 · V8 · O2 (flagship: best audio and multilingual vocals) · V7.6 (finer prompt control). V7, O1 and V6 are retired.

**Input:** A style description (comma-separated genres, moods, vocals), lyrics, a vocal gender, and optionally a reference track, a voice or a melody motif.

- Style description 1,000 characters, lyrics 5,000, title 50. Songs up to about 5 min; two versions per request.
- A melody motif cannot be combined with a reference track, a voice or a style description.
- Keep the style description to comma-separated descriptors; put vocal gender in its own field.
- Use O2 for non-English vocals.

**Rights:** Generation needs a paid plan for most models; check the plan's commercial terms.

Sources: [Mureka API (models)](https://github.com/useapi/mureka-api) · [Mureka create-advanced parameters](https://useapi.net/docs/api-mureka-v1/post-mureka-music-create-advanced)

## ElevenLabs (Eleven Music)

**Models:** music_v2_5 (recommended) · music_v2 · music_v1.

**Input:** A simple prompt, or a composition plan: a list of sections, each with a duration, styles to include, styles to avoid and lyrics. Can force an instrumental.

- Total length 3 s to 10 min, exact to the millisecond; each section 3 s to 2 min.
- Lyrics: at most 30 lines per section and 200 characters per line. Section text conventions: [Verse 1] for section names, (ooh) parentheses only for vocal sounds, {guitar solo} curly braces for short inline cues.
- Styles: up to 50 positive and 50 negative per section; the first section's styles set the tone, so give it at least 6-7 specific ones (e.g. '80 BPM', 'D minor', 'warm fingerpicked acoustic guitar'). context_adherence (low / medium / high, default high) sets how closely a section follows its neighbours.
- music_v1 takes global and per-section styles; v2 and v2.5 take a list of chunks and always respect section durations.
- Use a composition plan when timing matters (ads, intros, loops, scenes); plans are free to draft.
- Artist names, band names and copyrighted lyrics are rejected (the error suggests a rewrite): describe the sound.

**Rights:** Commercial use depends on the ElevenLabs plan.

Sources: [Eleven Music compose API](https://elevenlabs.io/docs/api-reference/music/compose) · [Eleven Music quickstart](https://elevenlabs.io/docs/eleven-api/guides/cookbooks/music) · [Eleven Music composition plans](https://elevenlabs.io/docs/eleven-api/guides/how-to/music/composition-plans)

## MiniMax (MiniMax Music)

**Models:** Music 3 (Aug 2026, open weights; songs up to 5 min) · Music 2.5 / 2.6 (hosted API, closed to new users since 20 Aug 2026).

**Input:** A music description and lyrics. For control, write the description in three parts: global (genre, BPM, key, scale, emotional arc, setting, production), vocal (gender, timbre, delivery, harmonies, effects), arrangement (instruments, how sections evolve, groove, textures).

- Music 3: tags [Intro] [Verse] [Pre-Chorus] [Chorus] [Post-Chorus] [Bridge] [Instrumental] [Solo] [Outro], each on its own line; put ## before each big lift (verse to chorus, into a drop), not on every line; prompt up to 5,000 tokens; English is the officially supported language.
- Music 2.5: lyrics 1-3,500 characters including tags, description up to 2,000; tags include [Pre Chorus], [Hook], [Build Up], [Break], [Inst], [Transition], [Interlude].
- Lines of 6-12 syllables; long lines get squeezed into the melody.
- Use one tag name per section type throughout ([Chorus], not alternating with [Hook] or [Refrain]).

**Rights:** Music 3 weights allow commercial use with attribution to MiniMax Music 3; projects above 20 million USD revenue need a separate agreement.

Sources: [MiniMax Music 3 model card](https://huggingface.co/MiniMaxAI/MiniMax-Music3/blob/main/README.md) · [MiniMax Music 3 lyrics format](https://minimaxmusic3.net/guides/lyrics-format) · [MiniMax Music 2.5 API](https://fal.ai/models/fal-ai/minimax-music/v2.5/api)

## YuE2 (open source (M-A-P))

**Models:** YuE2 (Sep 2026, 3B, open source; writes a score first) · YuE v1 (original).

**Input:** A style text (genre, instruments, vocal character, language, tempo) and lyrics with section tags. Score mode: full (editable melody and chords), melody, or off.

- YuE2 tags are lowercase: [verse] [chorus] [bridge] [outro]. Keep notes and instructions out of the lyrics.
- Local builds default to 30-second songs. YuE v1 used a five-part genre tag: genre, instrument, mood, gender, timbre.
- Use score mode 'melody' for covers, 'full' when the user wants to edit notes and chords.

**Rights:** Open source; runs locally.

Sources: [YuE / YuE2 (M-A-P)](https://github.com/multimodal-art-projection/YuE) · [YuE2 skill (official)](https://github.com/multimodal-art-projection/YuE/blob/main/skills/yue2-music/SKILL.md)
