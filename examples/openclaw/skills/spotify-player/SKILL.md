---
name: spotify-player
description: Terminal Spotify playback/search via spotify-safe-play and spogo.
homepage: https://www.spotify.com
metadata:
  {
    "openclaw":
      {
        "emoji": "🎵",
        "requires": { "anyBins": ["spotify-safe-play", "spogo"] }
      },
  }
---

# spotify-safe-play / spogo

Use `spotify-safe-play` as the default command for Spotify music playback in this environment.

Requirements

- Spotify Premium account
- `spotify-safe-play` and `spogo` installed
- Prefer an active Spotify Connect target such as `Web Player (Chrome)`

Preferred commands

- Search track: `spogo search track "query"`
- Search album: `spogo search album "query"`
- Search playlist: `spogo search playlist "query"`
- Safe playback: `spotify-safe-play <spotify-uri-or-url-or-id> [--device "..."]`
- Pause / resume / next / previous: `spogo pause`, `spogo play`, `spogo next`, `spogo prev`
- Devices: `spogo device list`, `spogo device set "<name|id>"`
- Status: `spogo status`

Playback rules

- For tracks, `spotify-safe-play` safely queues the requested track and skips to it
- For albums and playlists, `spotify-safe-play` queues tracks in normal order, enters the first track, then stops immediately
- After the first target track starts, do not send extra `next`, `play`, or verification loops
- Avoid direct `spogo play <track>` on affected machines because it may resume the current context instead of switching to the requested content
- Do not invoke `spotify_player` CLI commands on affected WSL setups when the auth flow is known to be unreliable
