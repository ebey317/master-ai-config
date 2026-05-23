---
name: project-sunkissed-security-privacy-architecture
description: "Sunkissed Soul Security module is privacy-first, 100% local default — vision via Ollama llava only, no cloud surveillance vendors. One bulb cam + one webcam constraint. Identity-driven design (operator: \"a Black man set up a digital house\")."
metadata: 
  node_type: memory
  type: project
  originSessionId: 05017a5d-eb68-4621-983a-d4f7e91b8fbd
---

Operator's stated architecture for Sunkissed Soul Security module (2026-05-22):

**Hardware reality (do not design beyond this):**
- ONE bulb security camera (WiFi, light-bulb form factor; usually RTSP or vendor protocol; assume `rtsp://<lan-ip>/live` until model confirmed)
- ONE USB webcam attached to Madam-Mary
- That's it. No fancy IP cam arrays. Minimal hardware doing maximum work via good software.

**Privacy ethos (non-negotiable):**
- Default mode: **100% LOCAL**. Vision analysis runs through Ollama's `llava:latest` at HubConfiguration.ollama_url. No Anthropic/Google/OpenAI vision in default mode.
- No camera frames leave the hub by default. Period.
- Cloud-assist on Security is OPT-IN per individual alert, never globally on.
- A visible "🔒 100% LOCAL" badge stays in the Security header whenever local_only_mode is on.
- Settings page must show a "Network privacy" panel listing every outbound connection from Security (zero in local-only mode).

**Identity-driven framing:**
- Operator: "you got me a black man set up a digital house the whole world about it because everybody knows how much I value privacy" — pride in the all-local design is part of the product narrative, not just an implementation detail. The privacy story is the brand story.

**Network model:**
- Local LAN: full functionality, no internet required.
- Remote access: via the existing Tailscale tunnel (operator's IP 100.101.249.96) — that's the VPN layer the operator referenced ("put a VPN on that"). Not the app's job to provision the VPN — already running.
- Internet is only used when explicitly opted-in (cloud-assist on a specific event, or model updates).

**Multifunction expectation:**
- Perimeter (bulb cam): sunrise/sunset auto-scan with llava ("anything new vs yesterday?"), motion-pushed events from the cam's own WiFi callback, light-on command if cam supports it (for spotlight-as-deterrent).
- Indoor (webcam): always-on motion via frame diff, llava check on motion, fires panic flow on threat detection (if silent_alert_enabled).
- Panic button: snapshot from BOTH cameras, email all emergency_contacts via Gmail MCP with images attached, create critical SecurityEvent, audible alarm via SpeechSynthesis, light up the bulb cam.

**How to apply:** When working on anything Security-related in Sunkissed Soul, default every choice toward local processing. Treat any "would be easier with a cloud API" thought as a red flag, not a shortcut. If a feature genuinely can't be done locally, surface it as an opt-in and explain WHY internet is needed for that one thing.
