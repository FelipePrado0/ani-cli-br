<p align=center>
<br>
<a href="https://github.com/pystardust/ani-cli"><img src="https://img.shields.io/badge/fork%20of-pystardust%2Fani--cli-blue"></a>
<a href="LICENSE"><img src="https://img.shields.io/badge/license-GPL--3.0-brightgreen"></a>
<a href="#Linux"><img src="https://img.shields.io/badge/os-linux-brightgreen">
<a href="#MacOS"><img src="https://img.shields.io/badge/os-mac-brightgreen">
<a href="#Android"><img src="https://img.shields.io/badge/os-android-brightgreen">
<a href="#Windows"><img src="https://img.shields.io/badge/os-windows-yellowgreen">
</p>

<h3 align="center">
ani-cli-br: fork of <a href="https://github.com/pystardust/ani-cli">ani-cli</a> with support for multiple sources, including Brazilian sites.
</h3>

<p align="center"><a href="README.pt-BR.md">Leia em Português</a></p>

## About this fork

This repository is a **fork** of the original [ani-cli](https://github.com/pystardust/ani-cli), created and maintained by [pystardust](https://github.com/pystardust) and contributors, licensed under **GPL-3.0**. All credit for the project's foundation (scraping architecture, history system, player integration) goes to the original authors. See [LICENSE](LICENSE) for the full license text.

The original project **does not accept** support for multiple sites, by the maintainers' deliberate decision (see [hacking.md](hacking.md)). This fork exists specifically to do that: extend ani-cli with additional sources and quality-of-life features, staying compatible with the original workflow whenever possible.

If you just want the original ani-cli (anidb.app source, actively maintained by the original authors), use the [official repository](https://github.com/pystardust/ani-cli).

## What's different in this fork

- **Multiple content providers** (`-p`/`--provider`): `anidb` (original), `animefire`, `animesdigital`. Interactive selection menu, or skip straight to one with the flag
- **History marathon** (`--marathon`): plays the next episode of every unwatched series, in sequence
- **New-episode notifications** (`--watch`): one-off check meant to run from cron/systemd timer
- **Local stats** (`--stats`): total series/episodes watched, broken down by provider
- **Provider health check** (`--check-providers`): pings each site before you search
- **Batch download** (`--all` + `-d`): downloads an entire series in one pass
- **History export/import** (`--export`/`--import`)
- **Optional config file** (`~/.config/ani-cli/config`), alongside env vars
- **Local cache** for search/episode lists (configurable TTL). Video links are never cached, they're signed and expire
- **Anime info card**: synopsis, genres and status (airing/finished) shown before playback
- **Fuzzy fallback search**: no exact match, retries with a shorter query
- **Synopsis preview in fzf** when picking an anime (anidb provider)
- **Rewatch confirmation** for episodes you've already seen
- Configurable retries and timeout, sanity guard on self-update

See the commit history for details on each change.

### Supported sources and known limitations

| Provider | Sub | Dub | Note |
|---|---|---|---|
| `anidb` (default) | ✅ | ✅ | Original source, per-episode sub/dub toggle |
| `animefire` | ✅ | ✅ | Dubbed is a separate search result (filtered automatically with `--dub`) |
| `animesdigital` | ✅ | ✅ | Same dub caveat as animefire |

Brazilian sites investigated and **not supported**, due to a real third-party blocker (documented in the commit history): Anroll and Goyabu (video sits behind the Google Blogger player, which now only runs client-side via JavaScript, no way to resolve it without a real JS engine), BetterAnime (same cause), Animes Vision (the player's video host went offline), AnimesOrion (site itself is down). This can change if those sites update their infrastructure, it's not laziness, it's a verified technical blocker.

Season switching (`change_season`) only works on the `anidb` provider, the others don't expose that relationship reliably.

History, `-c`, `--marathon` and `--watch` already know which provider each series belongs to (saved automatically).

## Installation

This fork isn't published to any package manager (scoop/brew/AUR/apt belong to the original project). Install from source:

```sh
git clone https://github.com/FelipePrado0/ani-cli-br.git
cd ani-cli-br
sudo cp ani-cli /usr/local/bin   # linux/mac
# or copy ani-cli to any directory on your PATH
```

On Windows, use Git Bash + [scoop](https://scoop.sh/) just for the dependencies (`fzf`, `mpv`), not for ani-cli itself. See the [Windows](#windows) section below.

### Windows

1. Install [scoop](https://scoop.sh/) and Git for Windows
2. `scoop bucket add extras && scoop install fzf mpv`
3. Clone this repository and run `ani-cli` from Git Bash (`sh ./ani-cli`, or add it to your PATH)

fzf hanging at `Search anime:` is a terminal issue (mintty), not a fork issue. Use Windows Terminal with the Git Bash profile. See the "Known Problems" section of the [original README](https://github.com/pystardust/ani-cli#windows-known-problems-and-solutions) for more.

## Dependencies

- `grep`, `sed`, `curl`
- `mpv`: video player (or `iina` on Mac, `vlc` as an alternative)
- `fzf`: interactive menu (or `rofi`/`dmenu`)
- `yt-dlp` or `ffmpeg`: only needed for `-d`/download
- `patch`: only needed for `-U`/self-update
- `ani-skip`: optional, for skipping intros (`--skip`)

If you get `Blocked by cloudflare`, install [curl-impersonate](https://github.com/lwthiker/curl-impersonate).

## Usage

```sh
ani-cli [options] [query]
```

### Main options

```
  -p, --provider          Content source (anidb, animefire, animesdigital)
  -q, --quality           Video quality
  -e, --episode, -r       Episode or range (e.g. -e 5-8)
  --all                   Select every episode (use with -d to batch-download a series)
  --dub                   Play the dubbed version
  -d, --download          Download instead of playing
  -c, --continue          Continue from history
  --marathon              Play the next episode of every series in history, in sequence
  -S, --select-nth        Pick the Nth result without an interactive menu
  -v, --vlc               Use VLC
  -s, --syncplay          Watch together via Syncplay
  --skip                  Skip intro (ani-skip, mpv)
```

### Utility commands

```
  --stats                 Local watch stats from history
  --check-providers       Check which sources are currently reachable
  --export <file>         Export history
  --import <file>         Import/merge history
  -D, --delete            Delete history
  --watch                 One-off check for new episodes (for cron/systemd timer)
  -N, --nextep-countdown  Countdown to the next episode
  -U, --update            Update the script (pulls from the configured branch)
  -V, --version           Version
  -h, --help              Full help
```

### Examples

```sh
ani-cli -p animefire --dub one piece
ani-cli -p animesdigital -q 720p banana fish
ani-cli --marathon
ani-cli -d --all mushoku tensei III
ani-cli --check-providers
ani-cli --stats
```

## Configuration

Besides the `ANI_CLI_*` environment variables (same convention as the original project: `ANI_CLI_QUALITY`, `ANI_CLI_PLAYER`, `ANI_CLI_MODE`, etc), this fork reads an optional config file:

```sh
# ~/.config/ani-cli/config  (or $ANI_CLI_CONFIG)
ANI_CLI_QUALITY=720
ANI_CLI_PROVIDER=animefire
ANI_CLI_CACHE_TTL=10
```

New variables in this fork: `ANI_CLI_PROVIDER`, `ANI_CLI_CACHE_DIR`, `ANI_CLI_CACHE_TTL`, `ANI_CLI_TIMEOUT`, `ANI_CLI_CONFIG`.

## FAQ

- **Can I change the subtitle language?** No. Subtitles are baked into the video, the site decides that, not ani-cli.
- **How does streaming work?** It plays straight from the URL, nothing is saved to disk (unless you use `-d`).
- **Does the cache store video?** No. Only search results and episode lists (short TTL). Video links are never cached, they're signed and expire.
- **Why isn't my favorite site on the list?** It was probably investigated and blocked by third-party infrastructure, see the limitations section above. Reverse-engineering contributions are welcome.

More general questions (install, players, etc): [original project's FAQ](https://github.com/pystardust/ani-cli#faq).

## Legal notice

This project accesses publicly hosted content from unaffiliated third parties, the same way a regular browser does, just more directly and specifically. Use at your own risk, subject to the laws of your country. See the original project's [disclaimer.md](disclaimer.md) for full details, including the DMCA/copyright process.

## License and credits

Licensed under **GPL-3.0**, same license as the original project. See [LICENSE](LICENSE).

- **Original project:** [pystardust/ani-cli](https://github.com/pystardust/ani-cli), by [pystardust](https://github.com/pystardust)/[port19x](https://github.com/port19x) and contributors
- **This fork:** multi-provider support, multi-source history, and the QoL utilities described above

Being GPL-3.0, this fork is and remains free software: you can copy it, modify it, and redistribute it, as long as you keep the same license and credit the original project. See the full text in [LICENSE](LICENSE).
