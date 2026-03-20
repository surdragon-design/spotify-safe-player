# Upstream Notes

This repository captures a practical workaround for a Windows desktop Spotify + WSL2 + OpenClaw setup.

## Observed problems

1. `spogo play <track>` could report success but fail to switch playback to the requested track on the active Spotify Connect target.
2. Direct context playback for albums and playlists was not reliable enough for consistent OpenClaw automation.
3. On some WSL machines, `spotify_player` could repeatedly prompt for OAuth instead of reusing a valid session cache.

## Stable workaround

- Track: `spogo queue add <track>` then `spogo next`
- Album / playlist: expand to track URIs, queue them in normal order, `spogo next` once, then stop
- Agent guidance: prefer `spotify-safe-play` plus `spogo`; avoid falling back to raw `spotify_player` when auth is unstable

## Good PR / issue framing

- For OpenClaw:
  - document the safer music playback path
  - ship a user-facing note that `spotify-safe-play` or equivalent queue-first logic is more reliable on affected setups
- For `spogo`:
  - report that `play` may resume existing context instead of switching to the requested item
  - discuss adding an optional queue-based fallback mode for music playback
