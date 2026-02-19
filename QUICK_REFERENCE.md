# SoulSync — Quick Reference

## Variables at a Glance

| Field | Container | Host (default) | Notes |
|-------|-----------|----------------|-------|
| WebUI Port | `8008` | `8008` | Access at `http://<server-ip>:8008` |
| Spotify OAuth Port | `8888` | `8888` | Required for Spotify login |
| Tidal OAuth Port | `8889` | `8889` | Required for Tidal login |
| Config | `/app/config` | `/mnt/user/appdata/soulsync/config` | App config files |
| Logs | `/app/logs` | `/mnt/user/appdata/soulsync/logs` | Log files |
| Database | `/app/data` | `/mnt/user/appdata/soulsync/data` | SQLite database storage |
| Downloads | `/app/downloads` | `/mnt/user/downloads/` | Must match slskd's completed downloads folder |
| Library/Transfer | `/app/Transfer` | `/mnt/user/library/` | Must match slskd's shared music folder |
| PUID | `PUID` | `99` | Unraid default (nobody) |
| PGID | `PGID` | `100` | Unraid default (users) |
| Timezone | `TZ` | `America/New_York` | Set to your local timezone |

---

## SoulSync GUI — Download Settings

These are the correct container paths to use inside the SoulSync settings UI. **Do not change them** — your host paths are mapped via the Unraid template or your docker compose.

| SoulSync GUI field | Value |
|--------------------|-------|
| Slskd Download Dir | `/app/downloads` |
| Matched Transfer Dir | `/app/Transfer` |
| Import Staging Dir | `./Staging` |

---

## slskd ↔ SoulSync Path Matching

Set up slskd with **incomplete** and **complete** download folders, then match:

| slskd folder | SoulSync setting |
|--------------|-----------------|
| Complete downloads | Downloads (`/app/downloads`) |
| Shared music / library | Library/Transfer (`/app/Transfer`) |

Both must point to the **same host folder** or files won't be found.

---

For troubleshooting and full setup instructions, see the [README](README.md).
