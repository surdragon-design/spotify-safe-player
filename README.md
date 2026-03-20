# spotify-safe-player

Safe Spotify playback wrappers and OpenClaw examples for Windows + WSL2 environments where direct `spogo play` is unreliable.

This repository packages the working approach we validated locally:

- Tracks: queue the requested track, then skip once
- Albums and playlists: expand the public Spotify page into track URIs, queue them in normal order, skip once into the first track, then stop
- OpenClaw: prefer `spotify-safe-play` and `spogo`, and avoid raw `spotify_player` CLI fallback on affected WSL setups

## Why this exists

In the tested setup:

- `spogo play <track>` often resumed the current playback context instead of switching to the requested song
- `spogo play <album|playlist>` was not reliable enough for consistent OpenClaw automation
- `spotify_player` OAuth/session handling inside WSL could trigger repeated re-auth prompts on some machines

The queue-based workaround turned out to be much more reliable for music playback requests.

## Repository layout

- [`bin/spotify-safe-play`](./bin/spotify-safe-play): the wrapper script
- [`skills/spotify-safe-play`](./skills/spotify-safe-play): the ClawHub-ready skill package
- [`examples/openclaw/skills/spotify-player/SKILL.md`](./examples/openclaw/skills/spotify-player/SKILL.md): a user-skill override example for OpenClaw
- [`docs/upstream-notes.md`](./docs/upstream-notes.md): concise notes for upstream OpenClaw / `spogo` discussions

## Requirements

- Spotify Premium
- `spogo` installed and authenticated
- An active Spotify Connect target such as Spotify Web Player or the desktop app
- Bash, `curl`, `grep`, and `awk`

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/surdragon-design/spotify-safe-player/main/bin/spotify-safe-play -o /usr/local/bin/spotify-safe-play
chmod +x /usr/local/bin/spotify-safe-play
```

If you use Linuxbrew on WSL, you can also install it into the existing user bin path:

```bash
install -m 755 ./bin/spotify-safe-play /home/linuxbrew/.linuxbrew/bin/spotify-safe-play
```

## Usage

```bash
spotify-safe-play spotify:track:7qiZfU4dY1lWllzX7mPBI3 --device "Web Player (Chrome)"
spotify-safe-play spotify:album:0E5yojmnEdbs68b1wM6Kla --device "Web Player (Chrome)"
spotify-safe-play spotify:playlist:37i9dQZF1DXcBWIGoYBM5M --device "Web Player (Chrome)"
```

Behavior summary:

- Track: `spogo queue add <track>` then `spogo next`
- Album / playlist: extract track URIs, queue them in normal order, run `spogo next` once, then exit immediately
- Show / episode: fall back to `spogo play`

## OpenClaw integration

The simplest rule for agents is:

```text
When asked to play Spotify music, prefer `spotify-safe-play` instead of calling `spogo play` directly.
Use `spogo search ...` for lookup and `spogo pause|play|next|prev|status|device ...` for transport and device control.
Do not fall back to raw `spotify_player` CLI commands on affected WSL setups.
```

A drop-in user-skill example is included under [`examples/openclaw`](./examples/openclaw).

## ClawHub

This repository also includes a publishable skill directory at [`skills/spotify-safe-play`](./skills/spotify-safe-play).

Typical publish flow:

```bash
clawhub login
clawhub publish ./skills/spotify-safe-play --slug spotify-safe-play --name "Spotify Safe Play" --version 0.1.0
```

## Known limitations

- Album and playlist expansion depends on the public Spotify page exposing track URIs in HTML
- This is a pragmatic wrapper, not a full Spotify client
- The exact failure mode of `spotify_player` auth under WSL can vary by machine

## Upstream direction

This repo is meant to support:

- an OpenClaw skill/docs PR that prefers the safe wrapper for music playback on affected setups
- a `spogo` issue or PR describing when `play` does not switch context reliably and how a queue-based fallback helps

## License

MIT
