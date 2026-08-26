# Changelog

Notable changes to HyperWhisper, from the point of view of using it.

The format follows [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Fixed

- **Turning the VAD filter off no longer fails.** It previously errored on
  every file longer than 30 seconds. Switching it off now runs sequential
  decoding: slower, but the transcript is correct, and the console says so.
- **Transcription no longer fails when a GPU is present but its CUDA runtime
  is not.** HyperWhisper now looks in every place those libraries reasonably
  live, and the installer checks they actually load rather than merely that a
  graphics card exists.
- **The installer no longer tries to install ffmpeg system-wide**, and no
  longer aborts when it cannot. A working ffmpeg ships with the application; a
  broken one already on your system is detected and stepped around.
- **Installing into a folder whose name is not plain English now works.**
  Accented, Cyrillic and CJK paths, and paths containing `%` or `&`, all
  install and launch correctly.
- **The installer no longer crashes when its output is redirected to a file.**
- **Install problems are now reported honestly.** A failed step no longer
  reports success, and the installer no longer claims the GPU is ready when
  the runtime behind it is missing.
- **A transcription that fails partway through now keeps what it decoded**,
  marked as partial, instead of discarding everything.
- **Re-running a job can no longer destroy the previous transcript** if the
  new write fails.
- **Error messages survive non-English filenames.** They were previously being
  discarded, leaving failures unexplained.

### Changed

- The startup console now names the ffmpeg and CUDA libraries actually in use,
  which makes problem reports far quicker to act on.
- The console panel opens compact instead of filling a third of the window.
- Settings dialog reworked: clearer spacing, no clipped labels.
- Telemetry readouts no longer overflow their pills; hover for the detail.
- Tooltips restyled for the dark interface and given a consistent width.
- The installer shows its banner before asking whether to proceed.

### Added

- A **Buy me a coffee** link, in the app's Settings dialog and in the README.

## 1.0.0

First release. Local Whisper transcription with a desktop interface: model
selection, GPU or CPU, batch queue, transcript viewer with editing, optional
speaker diarisation, and SRT / VTT / TXT / Premiere export.
