<div align="center">

<img src="assets/brand/logo.png" alt="HyperWhisper" height="220">

# HyperWhisper

**Turn any audio or video file into an accurate, timed transcript — on your own machine.**

Subtitles and transcripts from Whisper, with a real interface instead of a command line.
Nothing is uploaded, nothing is metered, and it keeps working offline.

### [⬇ Download the latest release](https://github.com/HyperWorX/HyperWhisper/releases/latest)

<a href="https://www.buymeacoffee.com/HyperWorX" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me a Coffee" height="48"></a>

</div>

---

## What it is

HyperWhisper is a desktop application for speech-to-text. You give it a media
file, it gives you back a transcript and subtitle files.

Under the hood it runs OpenAI's Whisper models through
[faster-whisper](https://github.com/SYSTRAN/faster-whisper) and
[CTranslate2](https://github.com/OpenNMT/CTranslate2), which are several times
faster and considerably lighter than the reference implementation. Around that
sits an interface that handles the parts a transcription job actually involves:
picking files, watching progress, reading the result, fixing the odd word, and
exporting it in the format you need.

**It is for you if** you caption videos, transcribe interviews or podcasts,
need searchable text from recordings, or simply want Whisper without living in
a terminal — and you would rather your recordings never left your computer.

### What it does

| | |
|---|---|
| **Runs locally** | No cloud, no API key, no per-minute cost, no upload. Works offline once the model is cached. |
| **Uses your GPU** | NVIDIA cards are detected and used automatically. No card, no problem — it falls back to CPU on its own. |
| **Shows real progress** | Model download and transcription both report actual percentages, with elapsed time and live CPU/GPU/RAM/VRAM readouts. |
| **Exports properly** | SRT, VTT, plain text, and a Premiere Pro-friendly SRT variant. Long lines are split into readable cues rather than dumped as one block. |
| **Handles a pile of files** | Queue up a whole folder and leave it running. |
| **Lets you fix things** | A built-in viewer with search and editing, so a misheard name does not mean starting over. |
| **Labels speakers** | Optional diarisation, using an ungated model that needs no account. |
| **Translates** | Any supported language into English, in one pass. |

### Supported formats

**Audio** — `aac` `aif` `aiff` `amr` `caf` `flac` `m4a` `m4b` `mka` `mp3` `oga` `ogg` `opus` `wav` `wma`

**Video** — `3gp` `avi` `flv` `m2ts` `m4v` `mkv` `mov` `mp4` `mpeg` `mpg` `mts` `ts` `vob` `webm` `wmv`

Video files have their audio extracted automatically; you do not need to
convert anything first.

---

## Install

Go to [**Releases**](https://github.com/HyperWorX/HyperWhisper/releases/latest)
and download the installer for your platform.

### Windows

Run `HyperWhisper-Setup-<version>.exe`. It installs to Program Files and adds a
Start Menu entry. You do not need Python; everything is bundled.

The installer has an opt-out task, **Set up NVIDIA GPU acceleration**. If an
NVIDIA card is found and the CUDA runtime is missing, it downloads about
553 MB of libraries. A machine with no NVIDIA card skips that download. You
can run it later from the Start Menu entry of the same name if you skipped
it or were offline at install time.

Each release publishes a SHA-256 alongside the installer if you want to verify
the download.

### Linux

Download the tarball, extract it, and run the `HyperWhisper` binary inside.

### ffmpeg

Bundled. You do not need to install it. If you already have ffmpeg on `PATH`
that one is preferred, unless it turns out to be broken, in which case
HyperWhisper quietly uses its own copy instead.

---

## Your first transcript

1. **Launch HyperWhisper.**
2. **Choose a media file.** Click **Browse** next to *Media File*, or drag the
   file anywhere onto the window.
3. **Choose where results go.** *Output Dir* — the transcript and subtitles are
   written here, named after your media file.
4. **Pick your output formats.** SRT and TXT are ticked by default.
5. **Press Start.**

The first run with a given model downloads it from Hugging Face. The default,
`large-v3-turbo`, is about 1.6 GB and is fetched once — later runs start
immediately from cache.

Watch the **Operator Console** at the bottom for live progress. When it
finishes, the **Viewer** tab opens with your transcript, and your chosen files
are in the output directory.

That is the whole job. Everything below is for when you want more control.

---

## The interface

Three tabs along the top.

### Transcribe

Where you set up and run a single job.

**Paths** — your input file and output directory.

**Model & Runtime**

| setting | what it does |
|---|---|
| **Model** | Which Whisper model to use. See [Choosing a model](#choosing-a-model). |
| **Device** | *Auto* picks your GPU when you have one. Force CPU here to rule a GPU issue out. |
| **Task** | *transcribe* keeps the original language. *translate* renders it into English. |
| **Language** | *auto* detects it. Setting it explicitly is faster and stops the occasional misdetection on short or noisy clips. |
| **Precision** | *auto* is right almost always. It picks the fastest numeric format your hardware supports. |
| **Timestamps** | *chunk* gives sentence-level timings, which is what subtitles want. *word* gives per-word timings — slower, useful for karaoke-style captions or precise editing. |
| **Batch Size** | How many audio windows are decoded at once. Higher is faster on a strong GPU, up to your VRAM. Lower it if you run out of memory. |
| **Condition** | Feeds each window the previous window's text for context. Better flow; occasionally lets a repetition loop run on. Turn it off if you see repeated text. |
| **Temperature** | Leave at 0.0 for the most literal output. Higher values let the model retry differently on windows it struggles with. |

**Output & Processing**

| setting | what it does |
|---|---|
| **Formats** | Which files to write. Tick as many as you like. |
| **Prompt** | Names, jargon or acronyms the model should expect. Genuinely effective — supply the spellings of people and products in the recording and it will use them. |
| **VAD Filter** | Voice Activity Detection. Skips silent stretches, which reduces hallucinated text on quiet audio. Leave it on. See [the note below](#a-note-on-the-vad-filter). |
| **Diarisation** | Speaker labels. See [Speaker diarisation](#speaker-diarisation). |

### Batch

Queue many files and leave it. Add files with **Browse Files** or by dragging
them in, then start the queue — it works through them one at a time using your
current Transcribe settings, and each file gets its own transcript and exports.

### Viewer

Opens automatically when a job finishes, or use **Open Latest** for the most
recent transcript, or **Open** for any transcript file.

Search the text, edit it in place, and save. Useful for correcting a misheard
name once rather than accepting it throughout — and the corrected text is what
gets exported.

---

## Choosing a model

Fastest first:

| model | when to use it |
|---|---|
| **`large-v3-turbo`** | The default, and the right answer most of the time. Several times faster than `large-v3` at close to the same accuracy. |
| `large-v3`, `large-v2` | When accuracy matters more than time: difficult audio, heavy accents, technical vocabulary. |
| `distil-large-v3.5`, `distil-large-v3` | English only, very fast, slightly less accurate. Good for bulk work. |
| `medium`, `small`, `base`, `tiny` | Progressively faster and less accurate. `tiny` is for testing your setup, not real work. |

Each model is downloaded once and cached.

---

## Output formats

| format | what it is | use it for |
|---|---|---|
| **SRT** | The universal subtitle format. | YouTube, VLC, most video editors. |
| **VTT** | WebVTT, the web standard. | HTML5 video, web players. |
| **TXT** | Plain text, no timestamps. | Reading, searching, quoting, feeding into other tools. |
| **Premiere** | An SRT variant tuned for Adobe Premiere Pro. | Importing captions into Premiere. |

Long segments are split into readable cues rather than left as walls of text,
so subtitles are actually usable as subtitles.

---

## A note on the VAD filter

Voice Activity Detection skips silence before transcription. It is on by
default and should normally stay on — it is what stops the model inventing text
during quiet passages.

If you turn it off, HyperWhisper switches to sequential decoding, because
batched inference takes its window splitting from VAD and cannot run without
it. The run is slower; the transcript is still correct. The console tells you
when this happens.

Turn it off only if VAD is clipping genuine speech in a noisy recording.

---

## Speaker diarisation

Tick **Diarisation**. Speakers are labelled in the transcript and in the
exported subtitles.

The default model is **ungated — no Hugging Face account or token required.**

If you switch to a gated `pyannote/*` model you will need a token: set
`HF_TOKEN` in your environment or enter it in Settings, and accept that model's
licence with the same account. The token is never written to disk and never
appears on a command line.

If you know how many people are speaking, say so in the speaker settings — it
improves the result noticeably.

---

## Where your files live

- **Settings** from your last run are restored automatically on next launch.
- **Presets** — save a named set of settings from the Settings dialog and load
  it later. Handy if you switch between, say, podcast and lecture workflows.
- **Logs** — the Settings dialog has a button to open the folder, which is the
  first place to look if something misbehaves.

These live in a per-user data directory, so nothing is written inside the
install folder.

---

## Troubleshooting

**Transcription fails with an ffmpeg error.**
Check the `ffmpeg:` line the app logs at startup — it names the exact binary in
use. A common cause is a broken package-manager shim: the file exists but its
target has been moved or uninstalled. HyperWhisper detects that and falls back
to its bundled copy.

**The transcript is empty.**
Almost always a silent or absent audio track — a muted recording, or a video
whose audio is on a stream that was not exported. The console says so
explicitly when it happens.

**Text repeats itself in a loop.**
Turn **Condition** off, or raise **Temperature** slightly.

**Names and jargon come out wrong.**
Put the correct spellings in the **Prompt** field.

**Out of memory on the GPU.**
Lower **Batch Size**, or choose a smaller model.

**It is using the CPU when I have an NVIDIA card.**
The startup console reports whether CUDA was found and which libraries it
resolved. Include that line if you report it.

---

## Support the project

HyperWhisper is free to use. If it saves you time, you can shout me a coffee —
there is a button for this in the app's Settings dialog too.

<div align="center">
<a href="https://www.buymeacoffee.com/HyperWorX" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me a Coffee" height="48"></a>
</div>

## Reporting issues

Open an issue at <https://github.com/HyperWorX/HyperWhisper/issues>. A short
description of what you did, what happened, and the relevant log from the logs
folder makes bugs far easier to fix.

## Licence

Proprietary. Copyright (c) 2026 HyperWorX, all rights reserved. You may
download, install and run HyperWhisper for your own use, including internal
business use; redistribution and resale are not permitted. See
[LICENSE](LICENSE) for the full terms.

This repository distributes HyperWhisper in built form. The source is not
published.

The third-party components HyperWhisper depends on remain under their own
licences, which are listed in section 5 of the licence file.

## Acknowledgements

- [faster-whisper](https://github.com/SYSTRAN/faster-whisper) — the transcription engine.
- [CTranslate2](https://github.com/OpenNMT/CTranslate2) — the inference runtime.
- [OpenAI Whisper](https://github.com/openai/whisper) — the model family.
- [pyannote-audio](https://github.com/pyannote/pyannote-audio) — speaker diarisation.
- [Flet](https://flet.dev/) — the cross-platform GUI framework.

---

If you use [Directory Opus](https://www.gpsoft.com.au/), you may also be
interested in [DopusWorX](https://github.com/HyperWorX/DopusWorX): a document
viewer and in-place editor that lives in the Opus pane.
