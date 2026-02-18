# SoulSync — Unraid Template

**Music discovery and automation for self-hosters.**
SoulSync finds new music, downloads it via Soulseek (FLAC-first), enriches metadata, and syncs your library to Plex, Jellyfin, or Navidrome — automatically.

[Support Thread](https://forums.unraid.net/topic/197283-support-soulsync/) · [Project](https://github.com/Nezreka/SoulSync) · [Docker Hub](https://hub.docker.com/r/boulderbadgedad/soulsync) · [Ko-fi](https://ko-fi.com/boulderbadgedad) · [Discord](https://discord.gg/wGvKqVQwmy)

---

## What is SoulSync?

SoulSync is a self-hosted music platform that bridges streaming services with your personal media server. Key capabilities:

- **Discovery**: Release Radar, Discovery Weekly, genre/decade mixes, Beatport charts, ListenBrainz playlists
- **Downloads**: Soulseek (FLAC priority), YouTube fallback, duplicate prevention, automatic retry
- **Metadata**: Spotify + iTunes enrichment, MusicBrainz matching, cover art embedding, LRC lyrics
- **Library tools**: Quality scanner, duplicate cleaner, album completion tracking, customizable folder templates
- **Integrations**: Apple Music, Spotify, Tidal, YouTube, Beatport, Plex, Jellyfin, Navidrome

---

## Prerequisites — Do This First

### 1. Install slskd (Soulseek client)

Install **slskd** from Community Applications **before** SoulSync. SoulSync uses slskd as its download engine.

When setting up slskd, configure these two folders on your Unraid host:

| Folder | Purpose | Example host path |
|--------|---------|-------------------|
| **Incomplete** | In-progress downloads | `/mnt/user/downloads/incomplete/` |
| **Complete** | Finished downloads | `/mnt/user/downloads/complete/` |

> **Tip:** Set up file sharing in slskd (`http://<server-ip>:5030/shares`) to avoid getting banned on Soulseek.

### 2. Generate an slskd API key

SoulSync authenticates to slskd via an API key. Add one to slskd's `settings.yml`:

```yaml
web:
  authentication:
    api_keys:
      soulsync:
        key: "your-api-key-here"
```

Restart slskd after saving, then copy the key — you'll need it during SoulSync setup.

---

## Path Matching — Critical

Both containers must point to the **same host folders**. This is the most common source of confusion.

| slskd setting | SoulSync setting | Same host path |
|---------------|-----------------|----------------|
| Completed downloads folder | Downloads (`/app/downloads`) | `/mnt/user/downloads/complete/` |
| Shared music / library folder | Library/Transfer (`/app/Transfer`) | `/mnt/user/Music/Library/` |

**Rule of thumb:**
> slskd completed download path **=** SoulSync Downloads path
> slskd shared music path **=** SoulSync Library/Transfer path

If these don't match, SoulSync won't be able to read downloaded files or organize your library.

---

## Installing SoulSync

1. Search **SoulSync** in Community Applications and click Install
2. Configure the template fields:

| Field | Default | Notes |
|-------|---------|-------|
| WebUI Port | `8008` | Access SoulSync at `http://<server-ip>:8008` |
| Spotify OAuth Port | `8888` | Required for Spotify login |
| Tidal OAuth Port | `8889` | Required for Tidal login |
| Config | `/mnt/user/appdata/soulsync/config` | Leave as default |
| Downloads | `/mnt/user/downloads/` | **Must match slskd's completed folder** |
| Library/Transfer | `/mnt/user/library/` | **Must match slskd's shared music folder** |
| PUID | `99` | Unraid default (nobody) |
| PGID | `100` | Unraid default (users) |
| Timezone | `America/New_York` | Set to your local timezone |

3. Click **Apply** and wait for the container to start.

---

## First-Run Configuration

Open SoulSync at `http://<server-ip>:8008` and go to **Settings**.

### Connect slskd (required)
- **slskd URL**: Use your server's LAN IP — e.g. `http://192.168.1.100:5030`
  - Do **not** use `localhost` — it won't resolve between containers
- **API Key**: Paste the key from slskd's `settings.yml`

### Connect Spotify (optional, apple fallback)
1. Create an app at [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Add Redirect URI: `http://127.0.0.1:8888/callback`
3. Copy **Client ID** and **Client Secret** into SoulSync settings

> **Accessing from a remote machine?** When Spotify redirects back, `127.0.0.1` points to *your browser's* machine, not the server. Manually replace `127.0.0.1` in the callback URL with your server's IP, or use SSH port forwarding: `ssh -L 8888:localhost:8888 user@<server-ip>`

### Connect a media server (optional)
- Use your server's LAN IP — e.g. `http://192.168.1.100:32400` for Plex
- After connecting, run **Database Update** from the Dashboard to populate your library

---

## Troubleshooting

Enable detailed logging: **Settings → Log Level → DEBUG** → check `logs/app.log`

| Problem | Solution |
|---------|----------|
| Discovery page is empty | Do a full **Database Scan**, then populate your **Watchlist** with artists. Discovery results build up and become more personalized over time |
| Library not showing up | Run **Database Update** from the Dashboard (do a full refresh on first run) |
| slskd won't connect | Use your server's LAN IP, not `localhost` or `127.0.0.1` |
| Files not organizing after download | Verify the Library/Transfer path is writable; check folder template syntax |
| Permission errors on files | Set PUID/PGID to match your Unraid user (default: 99/100) |
| Spotify OAuth fails from remote machine | Replace `127.0.0.1` with your server IP in the browser's callback URL |
| Authenticate button does nothing / API key not saving | Click **Save Settings** at the bottom of the Settings page *before* pressing Authenticate |
| Spotify auth fails entirely | Apple Music (iTunes) is used as an automatic fallback for metadata — no action needed |
| Downloads not found by SoulSync | Confirm slskd completed folder and SoulSync Downloads path point to the same host folder |
| Wrong track downloaded | Enable **AcoustID verification** in SoulSync settings |
| Wishlist not processing | Auto-retry runs on a timer; check logs for persistent failures |

---

## Useful Links

- [Unraid Support Thread](https://forums.unraid.net/topic/197283-support-soulsync/)
- [SoulSync GitHub](https://github.com/Nezreka/SoulSync)
- [Docker Hub](https://hub.docker.com/r/boulderbadgedad/soulsync)
- [Discord Community](https://discord.gg/wGvKqVQwmy)
