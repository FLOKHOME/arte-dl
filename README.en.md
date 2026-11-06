# arte-dl

*[Deutsch](README.md) | English*

Download movies and documentaries from the **ARTE Mediathek** from the
command line.

## Windows EXE (no installation)

Grab the latest `arte-dl-windows.exe` from the
[Releases](https://github.com/FLOKHOME/arte-dl/releases) page and run it —
**yt-dlp and ffmpeg are bundled** (no Python, no pip, no ffmpeg needed).
The console window stays open and shows progress.

```bat
arte-dl-windows.exe search "dark water"
arte-dl-windows.exe dl 133122-000-A
arte-dl-windows.exe dl 133122-000-A --audio ja --subtitles de
```

> Note: The EXE is unsigned — Windows SmartScreen will warn on first start
> ("Unknown publisher"). Click "More info → Run anyway". A `arte-dl-linux`
> binary is built as well; macOS users can run the tool from source.

## Installation (script usage)

```bash
# 1. wrapper into PATH (~/bin must exist; create with: mkdir -p ~/bin)
chmod +x ~/arte-dl/arte-dl.py
ln -sf ~/arte-dl/arte-dl.py ~/bin/arte-dl

# 2. yt-dlp is fetched automatically on first run
#    (needs only Python 3; ffmpeg must be installed)
```

## Commands

### Search
```bash
arte-dl.py search "dark water"
arte-dl.py search "ring"
```

### Show details (formats, audio languages, subtitles)
```bash
arte-dl.py info 133122-000-A
arte-dl.py info https://www.arte.tv/de/videos/133131-000-A/ring-das-original/
```

### Download
```bash
# Default: 1080p, German audio, to ~/Videos/arte
arte-dl.py dl 133122-000-A

# Japanese original audio + German subtitles
arte-dl.py dl 133122-000-A --audio ja --subtitles de

# 720p only, all subtitles as SRT
arte-dl.py dl 133131-000-A --max-height 720 --subtitles all

# Smaller files (H.264 instead of HEVC)
arte-dl.py dl 133131-000-A --quality klein

# Quality check: only the first 60 seconds
arte-dl.py dl 133131-000-A --test
```

## Options

| Option | Meaning |
|---|---|
| `--out DIR` | target folder (default: `~/Videos/arte`) |
| `--max-height N` | max. resolution: 432/720/1080 (default 1080) |
| `--audio LANG` | audio track: `de`, `fr`, `ja`, … (default `de`) |
| `--subtitles LANG[,...]` | save subtitles; `all` = all as SRT |
| `--quality best\|klein` | `klein` forces H.264 instead of HEVC |
| `--test` | download only the first 60 seconds |

## Requirements

- Python 3 (no pip needed)
- **ffmpeg** (for merging video and audio tracks)
- Internet access to arte.tv (search + player API), akamaized.net (video CDN)

## How it works

1. **Search**: the search page `arte.tv/de/search/?q=…` is server-side
   rendered (cards **and** typeahead list) — program IDs (`133122-000-A`),
   titles, type and description are parsed from it.
2. **Metadata/formats**: yt-dlp reads the ARTE player API
   (`api.arte.tv/api/player/v2/...`).
3. **Download**: HLS streams (video + separate audio languages) are merged
   into one MP4 via yt-dlp + ffmpeg. Multilingual films offer all audio
   tracks — `--audio` picks the desired one.
