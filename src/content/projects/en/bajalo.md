---
title: "Bajalo"
description: "Download videos and audio from the terminal with a CLI or a TUI, powered by yt-dlp."
pubDate: 2026-10-21
cover: "https://images.unsplash.com/photo-1611162616475-46b635cb6868?q=80&w=1074&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=NHwxMjA3fDA%3D"
tags: ["Python", "Textual", "yt-dlp"]
featured: true
links:
  - label: "GitHub"
    url: "https://github.com/thaishdz/bajalo"
    icon: "simple-icons:github"
toc: "center"
---

## Overview

Bajalo is a tool for downloading videos and audio from the terminal. It's built on top of [yt-dlp](https://github.com/yt-dlp/yt-dlp), so it works with any site yt-dlp supports (YouTube, Instagram and many more).

![Bajalo TUI after a finished download](/src/content/projects/es/_assets/bajalo-tui.svg)

yt-dlp is very powerful, but for something as common as "grab this video in 1080p as an mp4" you have to remember flags and formats. Bajalo wraps that complexity in two interfaces that only ask what they need:

- **`bajalo`** (or `bj`): an interactive CLI that asks a few questions and downloads.
- **`bajalo-tui`** (or `bjt`): a full-screen terminal UI with a form, a progress bar and themes.

The stack:

- **Language**: Python 3.11+
- **Downloads**: yt-dlp + ffmpeg (to merge video and audio and to convert audio)
- **TUI**: Textual
- **CLI**: questionary
- **Quality**: pytest + Ruff
- **CI**: GitHub Actions
- **Distribution**: pipx

## Goals

- **Download without memorizing flags**, picking type (video or audio only), folder, quality and format from menus.
- **Files that play anywhere**, preferring compatible codecs when mp4 is requested.
- **Work with vertical videos too** (reels, Shorts), not just horizontal ones.
- **One download core for two interfaces**, so the CLI and the TUI don't duplicate code.
- **One-command install** that doesn't clutter the system Python.

## How it works

### TUI

```bash
bjt
```

1. Paste the URL (it's validated as you type).
2. Choose **video** or **only audio** and where to save it.
3. Adjust the options and click **Download**.

### CLI

```bash
bj
```

It asks the same questions step by step. If you cancel with `Ctrl+C`, just run it again with the same URL to resume the download.

### Options

| Type  | Option  | Values                                  |
|-------|---------|-----------------------------------------|
| Video | Quality | 1080p, 720p                             |
| Video | Format  | auto, mp4, mkv, webm                    |
| Audio | Format  | auto (original quality), mp3, m4a, opus |

## Technical decisions

- **Shared core**: all the downloading lives in `downloader.py` (`download_video` and `download_audio`). The CLI and the TUI only collect options and call those functions; the TUI also hooks into yt-dlp's `progress_hooks` to draw the progress bar.
- **Quality measured on the short side**: formats are sorted with yt-dlp's `res` key, which measures the short side of the video. That way "1080p" works the same for horizontal and vertical videos.
- **Compatible mp4 over maximum resolution**: when mp4 is chosen, H.264 and AAC are preferred, even if that means a slightly lower resolution, so the file plays on any device. Anyone who wants the highest quality can pick **auto**.
- **Audio without downloading the whole video**: in audio mode only the audio track is fetched (`bestaudio`) and ffmpeg converts it to the chosen format.
- **The UI never blocks**: the download, the folder picker and opening the file manager run on separate threads using Textual workers, and update the interface with `call_from_thread`.
- **Native integration on each OS**: the folder picker uses Finder on macOS, the .NET dialog on Windows and `zenity`/`kdialog` on Linux. To reveal the downloaded file it uses `open -R`, `explorer /select` or the freedesktop D-Bus interface. If none of these are available (for example, without a graphical session), it falls back to typing the path by hand.
- **Remembers your preferences**: the theme and the last folder used are saved to `~/.config/bajalo/config.json` and restored when the TUI opens.

## Quality and CI

The project has tests for all four parts (CLI, TUI, downloader and utilities). The TUI tests use Textual's headless mode to simulate real interactions with the form.

On every push and pull request, GitHub Actions runs on Python 3.11 and 3.14:

- `ruff check` (lint)
- `ruff format --check` (formatting)
- `pytest`

## Install

```bash
pipx install git+https://github.com/thaishdz/bajalo.git
```

Requires [ffmpeg](https://ffmpeg.org/) (`brew install ffmpeg` on macOS).

> [!NOTE]
> Bajalo is developed and tested on **macOS**. It includes support for Linux and Windows, but it hasn't been tested on those systems yet.
