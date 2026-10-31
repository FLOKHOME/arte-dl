# arte-dl

Filme und Dokus aus der **ARTE Mediathek** per Kommandozeile herunterladen.

## Windows-EXE (ohne Installation)

Von [Releases](https://github.com/FLOKHOME/arte-dl/releases) die aktuelle
`arte-dl-windows.exe` laden und direkt starten — **yt-dlp und ffmpeg sind
bereits eingebaut** (kein Python, kein pip, kein ffmpeg nötig). Das Fenster
bleibt offen und zeigt den Fortschritt.

```bat
arte-dl-windows.exe search "dark water"
arte-dl-windows.exe dl 133122-000-A
arte-dl-windows.exe dl 133122-000-A --audio ja --subtitles de
```

> Hinweis: Die EXE ist nicht signiert — beim ersten Start meldet Windows
> SmartScreen „Unbekannter Herausgeber". Über „Weitere Informationen →
> Trotzdem ausführen" starten. Zusätzlich wird `arte-dl-linux` mitgebuildet;
> macOS-Nutzer starten das Tool aus dem Quellcode.

## Installation

```bash
# 1. Wrapper in PATH (~/bin muss existieren; vorher: mkdir -p ~/bin)
chmod +x ~/arte-dl/arte-dl.py
ln -sf ~/arte-dl/arte-dl.py ~/bin/arte-dl

# 2. yt-dlp wird beim ersten Aufruf automatisch geholt
#    (braucht nur Python 3; ffmpeg muss installiert sein)
```

## Kommandos

### Suche
```bash
arte-dl.py search "dark water"
arte-dl.py search "ring"
```

### Details anzeigen (Formate, Sprachen, Untertitel)
```bash
arte-dl.py info 133122-000-A
arte-dl.py info https://www.arte.tv/de/videos/133131-000-A/ring-das-original/
```

### Download
```bash
# Standard: 1080p, deutsche Tonspur, nach ~/Videos/arte
arte-dl.py dl 133122-000-A

# Japanische Originalspur + deutsche Untertitel
arte-dl.py dl 133122-000-A --audio ja --subtitles de

# Nur 720p, alle Untertitel als SRT
arte-dl.py dl 133131-000-A --max-height 720 --subtitles all

# Kleine Dateigröße (H.264 statt HEVC)
arte-dl.py dl 133131-000-A --quality klein

# Qualitätscheck: nur die ersten 60 Sekunden
arte-dl.py dl 133131-000-A --test
```

## Optionen

| Option | Bedeutung |
|---|---|
| `--out DIR` | Zielordner (Standard: `~/Videos/arte`) |
| `--max-height N` | max. Auflösung: 432/720/1080 (Standard 1080) |
| `--audio LANG` | Tonspur: `de`, `fr`, `ja`, … (Standard `de`) |
| `--subtitles LANG[,...]` | Untertitel mitspeichern; `all` = alle als SRT |
| `--quality best\|klein` | `klein` erzwingt H.264 statt HEVC |
| `--test` | nur die ersten 60 s herunterladen |

## Voraussetzungen

- Python 3 (kein pip nötig)
- **ffmpeg** (für den Merge von Video- und Audiospuren)
- Internetzugang zu arte.tv (Suche+Player-API), akamaized.net (Video-CDN)

## Wie es funktioniert

1. **Suche**: Die Suchseite `arte.tv/de/search/?q=…` wird serverseitig
   gerendert (Karten **und** Typahead-Liste) — daraus werden Programm-IDs
   (`133122-000-A`), Titel, Typ und Beschreibung geparst.
2. **Metadaten/Formate**: yt-dlp liest die ARTE-Player-API
   (`api.arte.tv/api/player/v2/...`).
3. **Download**: HLS-Streams (Video + separate Audiosprachen) via
   yt-dlp + ffmpeg zu einer MP4 gemerged. Mehrsprachige Filme liefern
   alle Tonspuren — `--audio` wählt die gewünschte.
