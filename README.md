# MMC - Media Meta Cleaner

A Dockerized **web application** that removes embedded metadata from video media files, with a modern React UI. Runs on Node.js in a container and is accessed from any web browser. Designed for Unraid and other Docker-based NAS environments.

## What it does

MMC scans directories for video files (`.mkv`, `.mp4`, `.avi`, `.mov`, `.wmv`, `.flv`), probes them for metadata tags (title, subtitle, tags, keywords, comment, description, synopsis), and provides a checkbox interface to selectively remove metadata from selected files.

It does **not** rename files, convert formats, or change encoding/quality. It only strips metadata tags so media servers like Plex and Jellyfin rely solely on filenames.

## Features

- **Modern web UI** - React + Tailwind, dark/light themes, responsive
- **Browser access** - Full application at `http://<host>:14500/`, no remote desktop streaming
- **Real-time progress** - WebSocket pushes scan %, batch %, and per-file copy % as they happen
- **Post-processing verification** - Every processed file is re-probed and reported as **Verified Clean** or **FAILED - metadata remains** (with remaining tags listed)
- **In-app logs** - Timestamped, color-coded log drawer with a final FAILED FILES summary
- **Processing mode selector** - Fast (In-Place) vs Safe (Copy)
- **Pause / Resume / Cancel** scanning

## Install on Unraid

MMC is available in the **Community Applications** store. In the Unraid web UI:

1. Open **Apps** and search for **MMC - Media Meta Cleaner**.
2. Click **Install**, then configure:
   - **Media** - path to your media directory, mapped to `/media`
   - **AppData** - application storage (settings.json), mapped to `/config`
   - **Web Port** - host port (default `14500`) mapped to container port `3000`
   - **TZ** - your timezone (optional)
3. Click **Apply** and open the WebUI from the Docker page (or visit `http://<host-ip>:14500/`).

## Docker install (non-Unraid)

### docker run

```bash
docker run -d \
  --name mmc \
  -p 14500:3000 \
  -v /path/to/media:/media \
  -v mmc-config:/config \
  --restart unless-stopped \
  galvatrondeva/mmc-media-meta-cleaner:latest
```

### docker compose

```yaml
services:
  mmc:
    image: galvatrondeva/mmc-media-meta-cleaner:latest
    container_name: mmc-media-meta-cleaner
    ports:
      - "14500:3000"
    volumes:
      - /path/to/media:/media
      - mmc-config:/config
    environment:
      - TZ=America/New_York
    restart: unless-stopped

volumes:
  mmc-config:
```

Open `http://<host-ip>:14500/` in any web browser.

## Configuration

| Setting | Target | Notes |
|---|---|---|
| Web Port | 3000 | Container port for the web UI (host port default 14500) |
| Media | /media | Media files to scan and process |
| AppData | /config | Persists `settings.json` (theme, mode, last directory) |
| TZ | - | Container timezone |

## Notes

- No authentication is built in; intended for private home networks. Put it behind a reverse proxy if you expose it publicly.
- v2.0 replaces the old desktop edition (v1.x), which streamed a PyQt6 UI over Xpra. This image is a native browser app - no Xpra/Xvfb/PyQt6 layers, smaller image, no remote desktop streaming.

## Support

Open an issue at https://github.com/Phive-5/mmc-media-meta-cleaner/issues for bugs, questions, or feature requests.

## License

MIT
