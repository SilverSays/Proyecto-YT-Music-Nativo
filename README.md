<img src="assets/readme/banner.png" alt="Motiv — a native YouTube Music client for Windows" width="100%">

# Motiv

*(full name Leitmotiv - Motiv for everyday use)*

A native desktop client for YouTube Music on Windows, built with Qt6 and `mpv`
instead of a bundled Chromium or Electron shell. It starts fast, stays around
~80-120 MB of RAM and has its own clean, cool-toned interface, with each
playlist's color pulled from its cover art. The interface is available in
Spanish and English.

> **Work in progress.** The app is already usable day to day, but I'm still
> adding features and polishing details. There may be big changes from one
> version to the next.

> **Legal notice.** This project is not official and has no relationship with
> YouTube, YouTube Music or Google. It uses their services in a way they don't
> intend, which may go against their [terms of
> service](https://www.youtube.com/t/terms). Using it is each person's own
> responsibility, including whatever may happen to their account: using a
> secondary account is recommended. The app streams music, just like the website;
> it's not meant for downloading or redistributing content, and must not be used
> for that. All content belongs to its creators.

## Contents

- 🎧 **User Guide** — [Screenshots](#screenshots) · [What it does](#what-it-does) ·
  [Keyboard shortcuts](#keyboard-shortcuts) · [Download and install](#download-and-install) ·
  [Setting up your account](#setting-up-your-account-optional) · [Status and reporting issues](#status-and-reporting-issues)
- 🛠️ **Developer & Contributor Guide** — [Architecture](#architecture) ·
  [Repository layout](#repository-layout) · [Local development](#local-development) · [Tests](#tests) ·
  [Building the installer](#building-the-installer) · [Design guidelines](#design-guidelines) ·
  [Contributing or forking](#contributing-or-forking)
- [License](#license)

---

## 🎧 User Guide

### Screenshots

<table>
<tr>
<td width="50%"><img src="assets/readme/screenshot-home.png" alt="Home screen: your playlists and recommendations picked from them"></td>
<td width="50%"><img src="assets/readme/screenshot-search.png" alt="Search results for an artist, with the artist card and the track list"></td>
</tr>
</table>

<img src="assets/readme/screenshot-now-playing.png" alt="The &quot;Now playing&quot; panel: large cover art, artist bio and similar music">

<img src="assets/readme/screenshot-mini-player.png" alt="The compact, always-on-top mini player" width="332">

### What it does

**Your library & music**
- **Your library**: syncs your YouTube Music playlists and stores them in a local
  database, so they open instantly and without connecting to the account.
- **Local playlists**: create playlists that only exist in the app, or save any
  playlist or album you find as your own.
- **Search without signing in**: songs, community playlists and artists, with
  suggestions as you type and recent searches.
- **Home**: your most-played playlists and recommendations of playlists similar to
  your music.
- **Artist page**: popular songs, albums, singles, playlists, similar artists and
  their bio.
- **"Now playing"** («Ahora suena»): large cover art, artist info and music similar
  to what's playing.
- **Synced lyrics** from [LRCLIB](https://lrclib.net) that follow the song line by line.
  When LRCLIB doesn't have them, the app asks YouTube Music (without your account)
  and shows which source each lyric comes from.
- **Music on your computer**: choose a music folder and its MP3, FLAC, M4A, AAC, OGG,
  Opus and WAV files appear under **En tu equipo**, with their tags and embedded
  cover art read straight from the files. They play directly from disk: no `yt-dlp`,
  no network, and they're never sent to Last.fm or to YouTube.

**Listening experience**
- **Listening history and stats**: every song you play for at least 30 seconds is
  saved locally on your device. It shows recent plays, most-played this month and
  all-time most-played tracks, and powers recommendations even without an account.
  You can disable it or clear it at any time.
- **Playback**: queue, shuffle, repeat, radio from a song or an artist, and
  preloading of the next tracks so there's no waiting. When you open the app,
  the queue and the song come back at the second you left them, stopped (this can
  be turned off in **Options**).
- **Loudness equalization (volume normalization)**: optional audio normalization
  using `mpv`'s `loudnorm` (EBU R128) or `dynaudnorm` filters, switchable on the fly
  without interrupting playback.
- **Sleep timer**: schedule playback to stop after 15, 30, 45, 60 minutes or when the
  current song ends. Gently fades out volume over the last 12 seconds and restores your
  listening volume once stopped. Can optionally exit the app.
- **Compact mini player** (`Ctrl+M`): a floating, frameless always-on-top window
  (332×104 px) with smooth marquee title scrolling, cover art, seek bar, and screen
  clamping so it never gets lost off-screen.
- **Windows media controls**: the app publishes its own System Media Transport
  Controls (SMTC) session, so the media keys on your keyboard or headphones work
  in the background, and the volume flyout and lock screen show the song with its
  cover art, its buttons and the progress bar.
- **Taskbar thumbnail toolbar**: hover over the taskbar button to access playback
  controls (Previous, Play/Pause, Next) directly within Windows' thumbnail preview,
  adapting cleanly to light and dark themes.
- **System tray & background playback**: closing the window minimizes to the system tray
  by default so music never stops. Includes a rich context menu with playback controls,
  mini player toggle, and an optional "Start with Windows" setting. Single-instance
  support restores the existing window when launched again.

**Look, language & accessibility**
- **Light and dark themes**: the "Plata" look also comes in a night version. By
  default it follows Windows (light or dark apps) and switches on the fly when you
  change it, without stopping the music.
- **Spanish or English**: the interface follows the Windows display language
  (Spanish if Windows is in Spanish, English otherwise) or the one you pick in
  **Options → General → Language**. The change applies when the app reopens; the
  **Restart now** button does it for you.
- **Options window** (**⋯** menu → **Options…**, or `Ctrl+,`): every preference in
  one place, grouped by topic (General, Playback, Account, Integrations,
  Maintenance). Each one shows what it does right under its name, toggles are real
  switches that a screen reader announces with their state, and everything works
  from the keyboard (arrows to change topic, `Tab` to go through its options,
  `Ctrl+Tab` for the next topic). The **⋯** menu keeps only everyday actions:
  sync, new playlist, music folder, sleep timer and view toggles.
- **Interface**: purposeful animations (sliding panels, cover art that travels from
  the card to the header, lists that appear staggered) that respect Windows'
  reduce-animations setting; full keyboard navigation.

**Privacy & integrations (all optional, off by default unless noted)**
- **Your credentials are encrypted**: account cookies and the Last.fm session are
  stored encrypted with Windows DPAPI, tied to your Windows user — see
  [Setting up your account](#setting-up-your-account-optional) for how signing in works.
- **Backup and restore**: export your local playlists, listening history, and settings
  to an atomic JSON backup file. Import back with options to merge or replace. Private
  by design: never exports credentials, session tokens, or cookies.
- **Resilience**: if YouTube changes something and playback breaks, the app updates
  `yt-dlp` by itself, checks that the new version works and rolls back if it doesn't.
- **Discord Rich Presence**: shows "Listening to" with the song, artist, cover art and
  progress in your Discord profile. It talks to the Discord desktop app through its
  local pipe, with no extra libraries; if Discord isn't running or closes, the app
  keeps playing and retries quietly in the background.
- **Last.fm scrobbling**: announces what's playing and scrobbles each song once half
  of it (or 4 minutes) has played. Without a connection, scrobbles wait in a local
  queue and are sent in batches when the service is back.
- **Changes to your YouTube Music account**: like songs, save them to or remove them
  from your library, copy a local playlist to your account, and optionally add what
  you listen to to your YouTube history. The app warns you before turning this on;
  while it's off, nothing is ever written to the account. Changes show at once and
  are undone with a notice if YouTube refuses them.
- **New version notice**: at most once a day the app asks GitHub for the latest
  release and, if there's a newer one, shows a small link at the bottom of the
  sidebar to its page. It never downloads or installs anything by itself, and it
  can be turned off.

### Keyboard shortcuts

| Shortcut | Action |
| :--- | :--- |
| Space | Play or pause |
| Ctrl+→ / Ctrl+← | Next / previous song |
| Ctrl+F | Search |
| Ctrl+L | Show lyrics |
| Ctrl+I | Show "Now playing" |
| Ctrl+M | Toggle mini player |
| Ctrl+B | Collapse or expand the sidebar |
| Ctrl+N | New local playlist |
| F5 | Sync library with account |
| Alt+← | Back to the previous view |
| Ctrl+Q | Exit application |

### Download and install

Download `Motiv-<version>-instalador.exe` from the
[Releases](https://github.com/SilverSays/Proyecto-YT-Music-Nativo/releases) page
and run it. It installs for your user only (no administrator rights), adds a
Start menu shortcut (and a desktop one if you want) and registers a regular
uninstaller in Windows Settings. The first time the app opens, it offers to
download `yt-dlp`, `deno` and `mpv` — the small, official playback tools it
relies on instead of bundling a browser engine.

When uninstalling, it asks whether to also delete your data in
`%LOCALAPPDATA%\YTMusicNativo`; answer "No" if you plan to reinstall.

Without an account you can already search, browse artists and listen to any song.
Prefer running it from the source code instead? See
[Local development](#local-development) in the Developer Guide.

### Setting up your account (optional)

You only need this to see **your library**. **Use a secondary account**, not your
main one.

#### Sign in with Firefox (recommended)

From **Options → Account → Sign in with Firefox** (the **⋯** menu at the bottom of
the sidebar → **Options…**), or the **Sign in** button of the notice at the top:

1. Click **Abrir Firefox**. A separate, empty Firefox window opens (its own
   temporary profile: it doesn't see your usual tabs, accounts or extensions).
2. Sign in to YouTube Music with the secondary account and wait until you see
   your library.
3. Close that Firefox window. The app reads the session cookies, checks that the
   session works, stores it encrypted and deletes that temporary Firefox profile
   from disk.

Firefox has to be installed, but it doesn't need to be your default browser.
Chrome isn't supported for this on purpose: recent versions bind the session to
the device (*Device Bound Session Credentials*) and encrypt their cookies with a
key only Chrome can use (*app-bound* encryption), so a session taken out of Chrome
stops working within a few hours.

**Your credentials are encrypted.** `browser.json`, `cookies.txt` and the Last.fm
session are stored encrypted with Windows DPAPI: only your Windows user, on this
computer, can read them. Files left in plain text by older versions are encrypted
automatically the next time the app starts. `yt-dlp` gets a readable copy of the
cookies only while it runs, under a random name, and that copy is deleted as soon
as it finishes (leftovers from a crash are removed at startup). If the files are
copied to another computer or Windows user, the app just asks you to sign in again.

<details>
<summary><strong>Manual setup and cookies for playback (advanced)</strong></summary>

**Manual setup**, from **Options → Account → Credentials and playback mode**:

1. Open a **Firefox private window** and sign in to
   [music.youtube.com](https://music.youtube.com) with that account. Use Firefox:
   Chrome ties the session to the device, and copied credentials expire within a few
   hours.
2. Open the developer tools (F12) → **Network** tab, filter by `browse` and reload
   the page.
3. Right-click a `browse` request → **Copy** → **Copy Request Headers**.
4. In the app, on the **Credenciales** (Credentials) tab, paste the headers and click
   **Guardar cabeceras** (Save headers).
5. Close the private window **without signing out** and don't open it again:
   signing out invalidates the copied credentials.

**Cookies for playback.** By default the audio is fetched without an account. If
YouTube blocks those requests, the app can use the account's cookies, but only
after you explicitly accept the risk (with cookies, playback is tied to the
account). Signing in with Firefox already stores them; you can also load a
`cookies.txt` (exported as explained in the [yt-dlp
guide](https://github.com/yt-dlp/yt-dlp/wiki/Extractors#exporting-youtube-cookies)).

</details>

#### Discord and Last.fm (optional)

Both are off by default and live in **Options → Integrations**.

- **Discord**: Discord needs the ID of an application registered at
  [discord.com/developers/applications](https://discord.com/developers/applications)
  (its name is what appears after "Listening to"). The first time you turn the
  option on, the app asks for that *Application ID*. Only the song title,
  artist, album, cover art and times are shared.
- **Last.fm**: choose **Connect…** next to **Last.fm account**. If the app doesn't come with
  its own Last.fm API key, it asks for one (create it for free at
  [last.fm/api/account/create](https://www.last.fm/api/account/create)); then it
  opens Last.fm in your browser so you can allow access. The key, the secret and
  the session are stored encrypted with Windows DPAPI in
  `%LOCALAPPDATA%\YTMusicNativo\credenciales\lastfm.bin`.

#### Account changes, music folder, theme and language

- **Options → Account → Changes to your YouTube Music account**: turn on **Allow
  changes to my YouTube Music account** to get the heart in the player bar and the like/library options in a
  song's right-click menu. Removing something asks for confirmation, and changes are
  sent one at a time with short pauses. **Record my plays in my YouTube history**
  is a separate option on top of it.
- **⋯ menu → Music on this computer**: choose the folder; only new or changed files
  are read again when the app opens.
- **Options → General**: **Theme** (*Same as Windows*, *Light* or *Dark*) and
  **Language** (*Same as Windows*, *Español* or *English*).

#### Where everything is stored

None of this is stored in the project folder or leaves your computer:

| What | Where |
| :--- | :--- |
| `yt-dlp`, `deno`, `mpv` | `%LOCALAPPDATA%\YTMusicNativo\bin` |
| Credentials (`browser.json`, `cookies.txt`, `lastfm.bin`, all encrypted) | `%LOCALAPPDATA%\YTMusicNativo\credenciales` |
| Library, cover art cache and logs | `%LOCALAPPDATA%\YTMusicNativo` |

To uninstall a copy run from the source code, delete the project folder and
`%LOCALAPPDATA%\YTMusicNativo`. The installed version is removed from Windows
Settings → Apps.

### Status and reporting issues

The app is under active development. I keep working on new features and on
polishing the interface and stability. If something breaks, open an *issue* saying
what you were doing and, if you can, attach the log from
`%LOCALAPPDATA%\YTMusicNativo\logs` (check it first: it may include song and
playlist names).

---

## 🛠️ Developer & Contributor Guide

### Architecture

The app is split into four decoupled layers:

- **`core/`**: the base everything else uses — data and credential paths, the
  SQLite database, encryption at rest with Windows DPAPI, and Windows Job Objects
  for process-tree management.
- **`playback/`**: everything that makes a song play — `yt-dlp` as the audio
  fetcher, `mpv` controlled over its own IPC socket, and the Windows System Media
  Transport Controls (SMTC) session published over WinRT.
- **`services/`**: online data and side features — the YouTube Music client,
  LRCLIB lyrics, local file tags, backups, Discord and Last.fm.
- **`ui/`**: the PySide6 (Qt6) interface. `MainWindow` is composed from mixins in
  `ui/views/` (one module per view: queue, search, history, local library,
  playlists), and visual pieces are split by family into their own modules under
  `ui/` instead of one or two large files.

### Repository layout

```
ytm_native/
├── __main__.py, app.py   Startup: creates the database, the player and the window
├── core/                 The base everything else uses (paths, db, crypto, i18n…)
├── playback/             Everything that makes a song play (mpv IPC, SMTC…)
├── services/             YouTube Music, LRCLIB data, and local backups
├── ui/                   The Qt interface: components by family, Options window, system tray
│   └── views/            MainWindow mixins, one module per view (queue, search, history…)
└── tools/                Development tools (not part of the app)
tests/                    Automated tests (no network, with a fake mpv and yt-dlp)
instalar.bat, iniciar.bat Install and launch on Windows from the source code
ytm_native.spec           PyInstaller build of the standalone program
installer.iss             Inno Setup script for the Windows installer
construir_instalador.bat  Builds both in one step
```

**`core/`: the base**

| File | What it does |
| :--- | :--- |
| `paths.py` | Data, binary and credential paths in `%LOCALAPPDATA%` (the `YTMN_HOME`, `YTMN_BIN_DIR` and `YTMN_CRED_DIR` variables override them). |
| `config.py` | Constants: timeouts, request limits, sync cadence. |
| `crypto.py` | Encryption at rest with Windows DPAPI (`CryptProtectData` / `CryptUnprotectData`). |
| `credentials.py` | Encrypted reading and writing of the account credentials, migration of plain-text files and the short-lived cookie copy for `yt-dlp`. |
| `db.py` | SQLite database: tracks, playlists, local playlists, settings and caches. |
| `errors.py` | Classifies `yt-dlp` errors (blocked, network, expired session, unavailable…) and their messages. |
| `i18n.py`, `i18n_en.py` | Interface language: Spanish is written directly in the code and `tr()` swaps it for the English catalog (`i18n_en.py`) when needed. |
| `instance.py` | Single-instance lock via local IPC socket and window foreground activation. |
| `proc_guard.py` | Windows Job Objects for process tree management and synchronous I/O cancellation. |

**`playback/`: playback**

| File | What it does |
| :--- | :--- |
| `extractor.py` | The audio *fetcher*: runs `yt-dlp` to get each song's URL, keeps a URL cache and updates `yt-dlp` with rollback if the new version fails. |
| `mpv_ipc.py` | Launches `mpv` as a separate process, controls it through IPC, and applies audio normalization filters. |
| `history.py` | Decides when a song counts as listened and records it in listening history. |
| `player.py` | The player: queue, shuffle, repeat, radio, preloading and error recovery. |
| `sleep_timer.py` | Sleep timer with smooth volume fade-out and track-completion stopping. |
| `session.py` | Saves the queue and the current second, and restores them on startup. |
| `bootstrap.py` | Downloads and installs `yt-dlp`, `deno` and `mpv`. |
| `media_controls.py` | Publishes the Windows System Media Transport Controls (SMTC) session over WinRT: title, artist, cover art and buttons. |

**`services/`: online data and backups**

| File | What it does |
| :--- | :--- |
| `sync.py` | Syncs the account's library with `ytmusicapi`, manages the session and searches the local library. |
| `auth_assistant.py` | Assisted sign-in: launches Firefox with a throwaway profile, reads its cookies and stores the session encrypted. |
| `discover.py` | Search, suggestions, home recommendations, other people's playlists, artist pages and the "Now playing" info. |
| `lyrics.py` | Lyrics from LRCLIB and, as a fallback, YouTube Music; which line is the current one. |
| `account_actions.py` | Optional changes to the account (likes, library, playlists, history) behind an off-by-default switch, with optimistic UI and rate limiting. |
| `local_media.py` | Music on disk: tag reader for ID3, Vorbis, FLAC, MP4 and WAV (standard library only) and the folder scanner. |
| `backup.py` | Atomic export and import of local playlists, listening history, and preferences. |
| `discord_rpc.py` | Optional Discord Rich Presence over Discord's local IPC pipe, in its own thread. |
| `lastfm.py` | Optional Last.fm scrobbling: API client, retry queue in SQLite and encrypted session. |
| `updater.py` | Once-a-day check of GitHub Releases for a newer version of the app (notice only). |

**`ui/`: interface**

| File | What it does |
| :--- | :--- |
| `main_window.py` | The window: ties together the views, the player and the panels, and decides what's shown. |
| `theme.py` | Colors (the "Plata" light and dark schemes), typography and stylesheet. It's the only place with color values. |
| `motion.py` | Curves, durations and `Tween`, the animated value every animation uses. |
| `settings.py` | The Options window: every preference in one place, grouped by topic, each with its switch/list/button and a visible one-line explanation. |
| `components.py` | Re-exports the family modules below (`buttons`, `cards`, `band`, `table`, `sidebar`, `notices`, `lyrics_panel`, `primitives`) so existing `from . import components` imports keep working. A new widget is added to its family module, not here. |
| `buttons.py` | Hand-painted buttons and sliders: press, hover fade, and a focus ring that only shows up when navigating with the keyboard. |
| `cards.py` | Cover art (its fade-in and the "flight" animation between views) and the playlist/artist cards and grid (`SleeveCard`, `CardGrid`, `ArtistStrip`). |
| `band.py` | Each view's header band (cover, title, notes, actions) and the home screen's shelf rows (`ShelfSection`). |
| `table.py` | The track table: view, row painting, filtering and the empty-state notice. |
| `sidebar.py` | Shared pieces of the navigation lists (sidebar and Options): the active-row mark, the focus ring and the sidebar footer. |
| `notices.py` | The fixed banner above a list and the notices that fade in and fade out. |
| `lyrics_panel.py` | The lyrics panel, with the line that's currently playing highlighted. |
| `primitives.py` | Painting and motion helpers shared by the pieces above: glyphs, reveal, visibility fades and clipped text. |
| `home.py` | The home screen layout. |
| `search.py` | Search parts: tabs and results grid. |
| `artist.py` | The artist page. |
| `now.py` | The "Now playing" panel. |
| `mini_player.py` | Compact floating mini player with smooth marquee text and screen clamping. |
| `tray.py` | System tray icon with multi-resolution vector rendering and Windows autostart. |
| `taskbar.py` | Native Windows COM (`ITaskbarList3`) thumbnail toolbar controls. |
| `widgets.py` | Background task runner, the track table model/filter, and the account dialog. |
| `dialogs.py` | The guided "Sign in with Firefox" dialog. |
| `art.py` | Cover art download and cache, and the color pulled from each one. |
| `chrome.py`, `focus.py` | Window-wide details: title bar, menus and a keyboard-only focus ring. |

**`ui/views/`: `MainWindow` mixins.** `MainWindow` is declared as
`class MainWindow(QueueView, SearchView, HistoryView, LocalView, PlaylistView, QMainWindow)`:
each view's logic lives in its own mixin module, not in `main_window.py`.

| File | What it does |
| :--- | :--- |
| `common.py` | View keys and small utilities shared by `MainWindow` and its view mixins. |
| `queue_view.py` | The playback queue: what's shown, clearing it, removing/reordering tracks, and queuing from any view. |
| `search_view.py` | Search: typing, suggestions, scope (songs, playlists or artists), and the table/card layout toggle. |
| `history_view.py` | Listening history: recent and most-played, with their counts, and the home screen's "Recently played" row. |
| `local_view.py` | Music on disk: the chosen folder, reading it, and what's shown from it. |
| `playlist_view.py` | Playlists: the library's own (local and the account's), other people's opened from Home, and the table's filter and sort. |

`tools/fase0.py` is the feasibility test the project started with: it
measures how many requests YouTube blocks with and without an account.

### Local development

Requirements: **Windows 10 or 11** and **Python 3.10 or later** (from
[python.org](https://www.python.org/downloads/), with the `py` launcher).

1. Download or clone this repository.
2. Run `instalar.bat`. It creates its own Python environment in
   `%LOCALAPPDATA%\YTMusicNativo\venv` and installs the dependencies
   (`PySide6-Essentials`, `ytmusicapi` and the `winrt` packages for the media
   controls). If you already had the app installed, run it again to add the
   missing ones.
3. Run `iniciar.bat` to open the app. If the environment doesn't exist yet,
   it installs it first.
4. The first time, a notice at the top offers to **Descargar ahora** (Download now)
   the programs the app uses for playback. They're downloaded from their official
   sources on GitHub and are not part of this repository:
   - [`yt-dlp`](https://github.com/yt-dlp/yt-dlp) (*nightly* channel): gets
     the audio URL.
   - [`deno`](https://github.com/denoland/deno): needed by `yt-dlp`.
   - [`mpv`](https://mpv.io) ([shinchiro](https://github.com/shinchiro/mpv-winbuild-cmake)
     build): plays the audio.

   They can also be downloaded from a terminal with
   `python -m ytm_native.playback.bootstrap`.

### Tests

```
%LOCALAPPDATA%\YTMusicNativo\venv\Scripts\python.exe -m pip install -r requirements-dev.txt
%LOCALAPPDATA%\YTMusicNativo\venv\Scripts\python.exe -m pytest tests
```

They don't touch the network or your data: they use a temporary folder.

### Building the installer

```
construir_instalador.bat
```

It creates a separate build environment in `%LOCALAPPDATA%\YTMusicNativo-construccion`,
packages the app with [PyInstaller](https://pyinstaller.org) (`ytm_native.spec`)
and, if [Inno Setup 6.3+](https://jrsoftware.org/isdl.php) is installed, compiles
`installer.iss` into `dist\Motiv-<version>-instalador.exe`. Without Inno
Setup it stops at the portable folder with `YTMusicNativo.exe`.

`yt-dlp`, `deno` and `mpv` are not bundled on purpose: `yt-dlp` updates itself
and needs a writable folder, and downloading them on first launch keeps the
installer small.

To ship your own Discord application ID or Last.fm API key with a build, set
`DISCORD_CLIENT_ID`, `LASTFM_API_KEY` and `LASTFM_API_SECRET` in
`ytm_native/core/config.py` before building.

### Design guidelines

- Colors, radii and typography only come from `ui/theme.py`.
- Every animation goes through `ui/motion.py`, lasts at most 300 ms and respects
  Windows' reduced animations. Nothing animates on keyboard actions that repeat
  a lot.
- Interface text is written in Spanish, in normal sentence case (no all caps), and
  passed through `tr()` so it can be translated; add its English version to the
  catalog in `core/i18n_en.py` in the same change.

### Contributing or forking

Contributions are welcome. Before a big change, open an *issue* to discuss it.
See [Architecture](#architecture) and [Repository layout](#repository-layout)
above for where things live, and [Tests](#tests) for how to verify a change.

## License

[GPL-3.0](LICENSE). You can use, study, modify and redistribute the code,
as long as the versions you distribute stay free under the same license.

It uses [PySide6](https://doc.qt.io/qtforpython/) (LGPL),
[ytmusicapi](https://github.com/sigma67/ytmusicapi) (MIT),
[PyWinRT](https://github.com/pywinrt/pywinrt) (MIT),
[yt-dlp](https://github.com/yt-dlp/yt-dlp) (Unlicense),
[mpv](https://mpv.io) (GPL/LGPL) and [deno](https://deno.com) (MIT), which are
installed separately and keep their own licenses. The bundled interface fonts,
[Space Grotesk](https://fonts.google.com/specimen/Space+Grotesk) and
[IBM Plex Sans](https://fonts.google.com/specimen/IBM+Plex+Sans)
(`ytm_native/ui/fonts/`), are licensed under the
[SIL Open Font License 1.1](https://openfontlicense.org/).
