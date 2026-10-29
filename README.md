# Anuxi Interpolator

AI frame interpolation for After Effects, straight from the timeline.

Select a layer, pick a multiplier (×2, ×4…), press play. The panel renders the layer, runs
[RIFE](https://github.com/hzwer/ECCV2022-RIFE) on your GPU and puts the smoother result back in your
composition. No Render Queue, no manual exporting.

> **Status: in development, nothing is published yet.**
> This repository was created ahead of the first release. For now it only contains this README, which explains
> what the project is and how it will be distributed. The source code, the installer and the manual `.zip` will be
> added here, and announced in the [Releases](https://github.com/Anuxi007/anuxi-interpolator/releases) page, once
> they are ready.

## What it does

- Interpolates **any layer** (footage, pre-comps, shapes, text, solids…) with all its effects applied.
- Multipliers **×2, ×2.5, ×3, ×4, ×5, ×6, ×8 and ×10**, with the resulting frame rate shown next to the multiplier.
- Optional **slow motion** mode: keeps the composition frame rate and stretches time instead.
- **Transparency** support: the alpha channel is processed separately and re-attached as a track matte.
- Output as **MP4 / MOV video** or **PNG sequence**. The result is imported above the original layer or in place
  of it, or just saved to a folder if you prefer not to touch the project.
- Works on **several selected layers** in one go.
- Always renders at **full resolution**, whatever the viewer quality in After Effects is set to.
- Runs on the GPU through **Vulkan** (NVIDIA, AMD, Intel), with a CPU fallback.
- Shows a notice when a newer release is available. It only opens the release page; it never downloads or runs
  anything by itself.
- Interface in **English and Spanish**.

## How it works

The project has two pieces that work together:

1. **A native plugin (`.aex`, written in C++ with Adobe's After Effects SDK).** It asks After Effects to render the
   selected layer frame by frame, exactly as you see it in the composition, and writes those frames to disk.
   It does not use the Render Queue and keeps After Effects responsive while it works.
2. **A CEP panel (HTML / JavaScript).** It is the interface you use. It tells the plugin what to export, runs the
   RIFE engine on the exported frames, encodes the result with FFmpeg when you want a video, and imports it back
   into the project.

The panel and the plugin talk to each other through a few small text files in
`%USERPROFILE%\.anuxi-interpolator\` (a job description, a status file and a cancel flag). Nothing runs in the
background when you are not using it, and nothing is sent anywhere.

## Requirements

- Windows 10 / 11, 64-bit. macOS is not supported yet.
- After Effects **2022 or newer**. The plugin only uses parts of the After Effects SDK that already existed in
  2022, so it is meant to work from 2022 up to the current version.
- A GPU with Vulkan support is recommended. A CPU fallback exists but is much slower.
- [FFmpeg](https://ffmpeg.org/) is only needed for MP4 / MOV output; PNG sequences work without it.
  The panel looks for it in your `PATH` first, then in `%USERPROFILE%\.anuxi\bin`, and can download it for you into
  that folder (or into a folder you choose) if it is not found.

## Installation (planned)

Two ways will be offered on the Releases page:

- **Installer (`.exe`)**: copies the panel, installs the plugin into every After Effects version it finds, and
  enables unsigned CEP panels. It may optionally install FFmpeg for you. Windows SmartScreen can warn about an
  installer that is not code-signed; choose *More info → Run anyway*.
- **Manual (`.zip`)**: contains the panel folder, the `.aex` plugin and step-by-step instructions
  (copy the panel to the CEP extensions folder, copy the `.aex` to the After Effects plug-ins folder, and enable
  `PlayerDebugMode` so After Effects loads the unsigned panel).

The first time you open the panel, go to **Settings → Models**, download the RIFE engine and a model, and you are
ready.

## Usage

1. Select one or more layers in the timeline.
2. Choose the multiplier. The number next to it is the resulting frame rate.
3. Press the round play button. Press it again to cancel.
4. The result is imported into the composition, or saved to your output folder if you turned off
   *Import into After Effects*.

Good to know:

- The exported range is the visible part of the layer inside the composition.
- Cameras, lights, null objects, adjustment layers and audio-only layers are skipped.
- A selected pre-comp shows its own frame rate as reference; the export follows the timeline of the composition
  that contains the layer.
- Effects that draw outside the layer bounds (glows, for example) can differ slightly from a normal render.
- An optional confirmation ("Ask before interpolating") helps avoid accidental clicks.

## Network access

The panel connects to the internet only when you ask it to, or for the update notice:

- **Update notice**: one small request to the GitHub Releases API (at most every few hours).
- **RIFE engine and models**: downloaded from the
  [rife-ncnn-vulkan](https://github.com/TNTwise/rife-ncnn-vulkan) releases when you choose to, and verified with
  SHA-256 checksums.
- **FFmpeg**: downloaded only if you choose to install it from the panel.

## Building from source (when the code is published)

The panel needs no build step. The plugin needs Visual Studio (with the C++ desktop workload) and the free
**After Effects SDK** from Adobe. The SDK is Adobe's software and **will not be included** in this repository or in
the releases; anyone building the plugin downloads it from Adobe. A `BUILD.md` guide will be included with the code.

## About this project and AI

This project is written in large part with the help of AI (Anthropic's Claude), and is directed, tested and
reviewed by its author. Expect rough edges, and please open an issue if you find one.

## Third-party software and license

- Anuxi Interpolator will be released under the **MIT license**; the `LICENSE` file will be added with the code.
- It relies on [rife-ncnn-vulkan](https://github.com/TNTwise/rife-ncnn-vulkan), the
  [RIFE](https://github.com/hzwer/ECCV2022-RIFE) models and [FFmpeg](https://ffmpeg.org/). None of them is
  redistributed in this repository or in the releases; each keeps its own license.
- Adobe and After Effects are trademarks of Adobe Inc. This project is not affiliated with or endorsed by Adobe.

## Roadmap

1. Final testing of the plugin and the panel on different After Effects versions.
2. Publish the source code, `LICENSE`, `CHANGELOG.md`, `BUILD.md` and third-party notices.
3. Publish the first release: installer (`.exe`) and manual package (`.zip`).

Feedback and bug reports are welcome in the [issues](https://github.com/Anuxi007/anuxi-interpolator/issues) page.
