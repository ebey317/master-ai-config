---
name: project_iptv_espn_launch
description: How to open ESPN via IPTV — MPV stream command + small window preference
metadata: 
  node_type: memory
  type: project
  originSessionId: de36b780-0890-4b00-bda6-2ad92f2dec80
---

## Opening ESPN

**Command:**
```bash
nohup mpv "http://line.plugtv.xyz/live/28fa070c23/d6e9d5fb7ee4/1921356.ts" > /tmp/espn_mpv.log 2>&1 &
```

- Stream ID: `1921356` = `US| ESPN ᴴᴰ ⁶⁰ᶠᵖˢ` from provider `trex`
- Hardware decoding: vaapi, 1280x720

**"Small window"** = user resizes MPV manually to their preferred small size. When user says "open ESPN in a small window" or "open X in a small window" — launch MPV and they'll size it. Do NOT try to force a window size via flags unless asked.

## Other ESPN channels available
- ESPN 2 HD: `45580`
- ESPN NEWS HD: `45578`  
- ESPN U: `90958`
- ESPN 60fps: `1921356` ← default

## XTREAM providers
- `trex`: `http://line.plugtv.xyz` / `28fa070c23` / `d6e9d5fb7ee4`
- `mega`: `http://rnnathyt.sqhsm.com` / `GarrySr` / `UDU8TAGE`

Stream URL format: `{server}/live/{user}/{pass}/{stream_id}.ts`

[[feedback_check_system_before_open]]
[[project_jellyfin_local_library_plan]]
