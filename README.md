<div align="center">

<img src="./app-icon.png" width="80" alt="Ghost Pepper">

# Ghost Pepper

**100% private** on-device voice models for speech-to-text and meeting transcription on macOS. No cloud APIs, no data leaves your machine.

<a href="https://github.com/matthartman/ghost-pepper/releases/latest/download/GhostPepper.dmg">
  <img src="https://img.shields.io/badge/Download_for_Mac-FF6600?style=for-the-badge&logo=apple&logoColor=white" alt="Download for Mac" height="40">
</a>

macOS 14.0+ · Apple Silicon (M1+) · Free & open source

[![GitHub stars](https://img.shields.io/github/stars/matthartman/ghost-pepper?style=social)](https://github.com/matthartman/ghost-pepper)
&nbsp;
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
&nbsp;
![100% Local](https://img.shields.io/badge/100%25-Local-FF6600)
&nbsp;
![50+ Languages](https://img.shields.io/badge/50%2B-Languages-blue)

</div>

## Features

- **Hold Control to talk** — release to transcribe and paste into any text field
- **Meeting transcription** — record calls with notes, transcript, and AI-generated summaries saved as markdown
- **Runs entirely on your Mac** — models run locally via Apple Silicon, nothing is sent anywhere
- **Smart cleanup** — local LLM removes filler words and handles self-corrections
- **Menu bar app** — lives in your menu bar, no dock icon, launches at login
- **Customizable** — edit the cleanup prompt, pick your mic, toggle features on/off

## How it works

Ghost Pepper uses open-source models that run entirely on your Mac. Models download automatically and are cached locally.

### Speech models

| Model | Size | Best for |
|---|---|---|
| Whisper tiny.en | ~75 MB | Fastest, English only |
| **Whisper small.en** (default) | ~466 MB | Best accuracy, English only |
| Whisper small (multilingual) | ~466 MB | Multi-language support |
| Parakeet v3 (25 languages) | ~1.4 GB | Multi-language via [FluidAudio](https://github.com/FluidInference/FluidAudio) |
| Qwen3-ASR 0.6B int8 (50+ languages) | ~900 MB | Highest multilingual quality, macOS 15+ required |
| Moonshine Tiny (43M) | ~170 MB | Fast, via [moonshine-mlx](https://github.com/kylehowells/moonshine-mlx) |
| Moonshine Small (147M) | ~590 MB | Balanced, via moonshine-mlx |
| Moonshine Medium (245M) | ~980 MB | Best Moonshine accuracy, via moonshine-mlx |

### Cleanup models

| Model | Size | Speed |
|---|---|---|
| **Qwen 3.5 0.8B** (default) | ~535 MB | Very fast (~1-2s) |
| Qwen 3.5 2B | ~1.3 GB | Fast (~4-5s) |
| Qwen 3.5 4B | ~2.8 GB | Full quality (~5-7s) |

Speech models powered by [WhisperKit](https://github.com/argmaxinc/WhisperKit). Cleanup models powered by [LLM.swift](https://github.com/eastriverlee/LLM.swift). All models served by [Hugging Face](https://huggingface.co/).

## Getting started

**Download the app:**
1. Download [GhostPepper.dmg](https://github.com/matthartman/ghost-pepper/releases/latest/download/GhostPepper.dmg)
2. Open the DMG, drag Ghost Pepper to Applications
3. Grant Microphone and Accessibility permissions when prompted
4. Hold Control and speak

> **"Apple could not verify" warning?** On macOS Sequoia, you may see a Gatekeeper warning the first time you open the app. Go to **System Settings > Privacy & Security**, scroll down, and click **Open Anyway** next to the Ghost Pepper message. Click **Confirm** in the popup. You only need to do this once.

**Build from source:**
1. Clone the repo
2. Open `GhostPepper.xcodeproj` in Xcode
3. Build and run (Cmd+R)

**Local CLI install for development:**
1. Create local secrets: `cp GhostPepper/Secrets.example GhostPepper/Secrets.swift` and fill in Google OAuth values if you want Calendar integration. The file is gitignored; placeholder values are enough for local builds.
2. Build with a local Apple Development certificate. Replace `<TEAM_ID>` with the team ID from your signing certificate:
   ```sh
   xcodebuild build -project GhostPepper.xcodeproj -scheme GhostPepper -configuration Debug -destination 'platform=macOS,arch=arm64' -derivedDataPath /tmp/ghost-pepper-debug-derived-data -skipMacroValidation -skipPackagePluginValidation ARCHS=arm64 ONLY_ACTIVE_ARCH=YES CODE_SIGN_STYLE=Automatic CODE_SIGN_IDENTITY='Apple Development' DEVELOPMENT_TEAM=<TEAM_ID> PROVISIONING_PROFILE_SPECIFIER=
   ```
3. Install the built app to the same path used by the release app. Note the space in `Ghost Pepper.app`:
   ```sh
   rm -rf "/Applications/Ghost Pepper.app"
   ditto --rsrc --extattr "/tmp/ghost-pepper-debug-derived-data/Build/Products/Debug/GhostPepper.app" "/Applications/Ghost Pepper.app"
   xattr -dr com.apple.quarantine "/Applications/Ghost Pepper.app" 2>/dev/null || true
   open "/Applications/Ghost Pepper.app"
   ```
4. If macOS privacy permissions do not carry over after reinstalling, reset the old TCC rows and grant permissions again:
   ```sh
   tccutil reset Accessibility com.github.matthartman.ghostpepper
   tccutil reset ListenEvent com.github.matthartman.ghostpepper
   tccutil reset Microphone com.github.matthartman.ghostpepper
   tccutil reset ScreenCapture com.github.matthartman.ghostpepper
   ```
   For Input Monitoring, use `+` in System Settings and select `/Applications/Ghost Pepper.app` if the app does not appear automatically.

## Permissions

| Permission | Why |
|---|---|
| Microphone | Record your voice |
| Accessibility | Global hotkey and paste via simulated keystrokes |

## Privacy audit

Every core feature runs 100% on your Mac — verified by AI code review. No trust required, just point Claude at the repo and ask.

| Feature | Status | What was checked |
|---|---|---|
| Speech-to-text | :white_check_mark: Local | WhisperKit/FluidAudio inference, no audio sent anywhere |
| Text cleanup | :white_check_mark: Local | Qwen LLM runs on-device via LLM.swift |
| Audio recording | :white_check_mark: Local | AVAudioEngine + ScreenCaptureKit, no streaming |
| Meeting transcription & storage | :white_check_mark: Local | Chunked transcription, markdown files on disk |
| Summary generation | :white_check_mark: Local | Local LLM summarization, no cloud API |
| OCR & screen capture | :white_check_mark: Local | Apple Vision framework, on-device |
| File storage | :white_check_mark: Local | Markdown to local filesystem, no cloud sync |
| Analytics & telemetry | :white_check_mark: None | No Firebase, Mixpanel, Sentry, or any tracking SDK |

**Optional cloud features** (disabled by default, require your own API keys): Zo AI chat, Trello integration, Granola meeting import. Model downloads are one-time from Hugging Face.

> **Verify it yourself:** run `cat PRIVACY_AUDIT.md` in Claude Code and ask it to review the codebase against the audit prompt. The [full audit](PRIVACY_AUDIT.md) includes the exact prompt and detailed file-level results.

## Good to know

- **Launch at login** is enabled by default on first run. You can toggle it off in Settings.
- **Everything stays local** — transcription history and recordings are stored on your Mac only. Nothing is sent to the cloud. You can clear history anytime in Settings.

## Acknowledgments

Built with [WhisperKit](https://github.com/argmaxinc/WhisperKit), [moonshine-mlx](https://github.com/kylehowells/moonshine-mlx), [FluidAudio](https://github.com/FluidInference/FluidAudio), [LLM.swift](https://github.com/eastriverlee/LLM.swift), [Hugging Face](https://huggingface.co/), and [Sparkle](https://sparkle-project.org/).

## License

MIT

## Why "Ghost Pepper"?

All models run locally, no private data leaves your computer. And it's spicy to offer something for free that other apps have raised $80M to build.

## Enterprise / managed devices

Ghost Pepper requires Accessibility permission, which normally needs admin access to grant. On managed devices, IT admins can pre-approve this via an MDM profile (Jamf, Kandji, Mosaic, etc.) using a Privacy Preferences Policy Control (PPPC) payload:

| Field | Value |
|---|---|
| Bundle ID | `com.github.matthartman.ghostpepper` |
| Team ID | `BBVMGXR9AY` |
| Permission | Accessibility (`com.apple.security.accessibility`) |
