# Spotify Track Analyzer

A single-page web app that analyzes any Spotify track. Paste a track URL to see BPM, musical key, Camelot code, energy, danceability, valence, and the full audio feature set — plus an interactive Camelot Wheel (rendered with D3) showing the track's key and its three harmonically compatible neighbors for DJ mixing.

Built as one HTML file with vanilla JavaScript. No frameworks, no build tools. Spotify-inspired dark UI.

## Features

- **Track header** — album art, track name, artist(s), album, release year
- **Audio analysis cards** — BPM, key + mode, Camelot code, energy, danceability, valence, acousticness, instrumentalness, liveness, loudness, duration, time signature
- **Camelot Wheel** — full 24-segment D3.js SVG with the current key highlighted and harmonically compatible keys dimmed
- **JSON export** — clean structured output with a one-click copy button

## Stack

- Single `index.html` file, vanilla JS + CSS
- [D3.js](https://d3js.org/) v7 via CDN (Camelot Wheel only)
- Spotify Web API (`/v1/tracks`) for track metadata
- [ReccoBeats](https://reccobeats.com/) for audio features

> **Why ReccoBeats?** Spotify deprecated `/v1/audio-features` for apps created after 2024-11-27. ReccoBeats returns the same feature set (tempo, key, mode, energy, danceability, valence, acousticness, instrumentalness, liveness, loudness, speechiness) with the same scales and key conventions. No auth required.

## Setup

1. **Clone the repo** and `cd` into it.

2. **Create a Spotify app** at [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard):
   - App name: anything
   - Redirect URI: `http://127.0.0.1:8080/callback` (required but unused)
   - API: Web API
   - Copy the **Client ID** and **Client Secret**

3. **Add your credentials:**
   ```bash
   cp credentials.example.js credentials.js
   ```
   Edit `credentials.js` and paste your Client ID and Secret. This file is gitignored.

4. **Start a local server** (required — browsers block `<script src="credentials.js">` on `file://`):
   ```bash
   python3 -m http.server 8080
   ```

5. Open **http://127.0.0.1:8080/** and paste a Spotify track URL.

## Camelot Wheel

The Camelot Wheel is a DJ-friendly key-notation system where harmonically compatible keys are adjacent. A track in key `8A` mixes smoothly with:

- `8B` — same root, opposite mode (relative major/minor)
- `7A` — one step counter-clockwise (same mode)
- `9A` — one step clockwise (same mode)

Minor keys map to **A** codes, major keys to **B** codes. The app computes the Camelot code from Spotify's `key` (0–11) + `mode` (0 = minor, 1 = major) using a full 24-slot lookup table.

## Security notes

- `credentials.js` is gitignored. Never commit it.
- The Client Credentials flow exposes your `CLIENT_SECRET` in the browser — fine for a local tool, **not safe to deploy publicly**. For a public deployment, proxy the token exchange through a server or switch to PKCE.
- If you paste your secret anywhere public (chat, issue, screenshot), rotate it immediately from the Spotify dashboard.

## Files

```
index.html                ← the app
credentials.example.js    ← credentials template (committed)
credentials.js            ← your real secrets (gitignored)
DESIGN.md                 ← Spotify design-system reference used for styling
.gitignore
```

## Limitations

- ReccoBeats doesn't return `time_signature` — that card shows `—`.
- ReccoBeats catalog coverage is good but not exhaustive. If a track is missing, you'll see a "no audio features" error.
- `/v1/audio-features` on a newly-created Spotify app will return 403 — this is an intentional Spotify policy change, not a bug.
