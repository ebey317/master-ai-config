---
name: feedback_check_system_before_open
description: "When user says \"open X\" — always check the system first before launching anything"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: de36b780-0890-4b00-bda6-2ad92f2dec80
---

When the operator says "open [anything]" — **check the system first**. Run `ls` and look at what's installed before deciding how to open it.

**Why:** The system has native apps, IPTV, media players, etc. that are better/faster than the browser. Don't default to the web when a local app is the right tool.

**How to apply:**
1. `ls /home/elijah/Desktop/` — check for `.desktop` launchers
2. `which <appname>` — check if binary exists
3. Check running services if relevant (`systemctl --user list-units --state=running`)
4. Then decide: local app, sensei browser, or shell command

## Installed media stack (as of 2026-05-23)

| App | Binary | Notes |
|-----|--------|-------|
| **Hypnotix IPTV** | `/usr/bin/hypnotix` | Desktop: `IPTV.desktop` — primary IPTV player |
| **Jellyfin** | `/usr/bin/jellyfin` | Running as systemd service — local media server |
| **MPV** | `/usr/bin/mpv` | Lightweight video player |
| **Chrome** | `/usr/bin/google-chrome` | Browser — currently running |

**For IPTV/live channels**: `hypnotix` (or click `IPTV.desktop`)  
**For local media**: Jellyfin (`jellyfin.service` is running)  
**For streams/URLs**: `mpv <url>` or Hypnotix

## MPV-First Rule — video / movie / live TV / channel keywords

> "if it seems like a video movie live TV or channel keyword try MPV first and look for a M3U playlist locally"

When the user says **open [anything that sounds like video/TV/movie/channel/stream]**:
1. Check `~/.cache/hypnotix/providers/` for cached M3U/XTREAM JSON — stream IDs are there
2. Build XTREAM URL: `{server}/live/{user}/{pass}/{stream_id}.ts`
3. Launch: `nohup mpv "..." > /tmp/mpv.log 2>&1 &`
4. Do NOT open browser, do NOT launch Hypnotix GUI unless explicitly asked
5. Do NOT force a window size — user resizes manually

Cached channel index: `~/.cache/hypnotix/providers/trex-all_stream_Live.json` (16,351 streams; grep by channel name)

[[feedback_sensei_tennis_lessons]]
