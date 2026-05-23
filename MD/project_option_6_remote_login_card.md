---
name: Master AI Option 6 — Remote Login Card
description: 2026-04-25 direction for menu option 6. It should be a live remote-access card, not just static links: show on/live/off/disconnected status, exact URLs, login help, and service health for phone access.
type: project
---

# Option 6 Remote Login Card

Elijah wants menu option 6 to act like a remote-login card.

It should show:

- Tailscale status: on/live/off/disconnected.
- This node name and Tailscale IP.
- iPhone/remote URL for Pupil.
- LAN URL when available.
- Service status for Pupil `:8080`, TTS `:5050`, and Ollama `:11434`.
- Whether `:8080` is merely listening or actually answering HTTP.
- Exact next action when disconnected.

Current known good phone URL after the 2026-04-25 fix:

```text
http://100.101.249.96:8080/pupil.html
```

Use `http://`, not `https://`.

Use the IP when MagicDNS/DNS health is bad.

## 2026-04-25 Incident

Tailscale was not the root problem. The tailnet was alive:

- `madam-mary` = `100.101.249.96`
- `iphone175` = `100.127.18.126`
- `tailscale ping 100.127.18.126` succeeded.

The real failure was `master-ai-ui.service`: port `8080` was listening, but
`curl http://127.0.0.1:8080/pupil.html` timed out. Restarting the user service
fixed both local and Tailscale access:

```bash
systemctl --user restart master-ai-ui.service
```

After restart:

- `http://127.0.0.1:8080/pupil.html` returned HTTP 200.
- `http://100.101.249.96:8080/pupil.html` returned HTTP 200.

Option 6 should diagnose this exact split: Tailscale up + service wedged.

