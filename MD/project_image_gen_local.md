---
name: Local image gen — baseline ~56s/image on this CPU
description: Master AI has local image gen via stable-diffusion.cpp. 56s/512×512 on i7-6700T (CPU-only, 4-step LCM, q4_0, TAESD). Drives Pupil-primary UX.
type: project
originSessionId: 134c8c08-6a45-4a61-81f3-bafcf73e16b9
---
Master AI ships local image gen via `stable-diffusion.cpp` running as a systemd user service (`sd-server.service`) on `127.0.0.1:7860`. Production config: SD1.5 + LCM-LoRA + TAESD + q4_0 quant-on-load, 4-step LCM at 512×512 → **~56s per image** on Madam-Mary (i7-6700T, CPU only, no Vulkan because Ubuntu 22.04 ships an old SDK that doesn't compile against current ggml-vulkan; punted).

**Why:** Latency is the design driver. 56s is too long for an inline Sensei chat reply, but well within Pupil's "rendering…" pop-up pattern. So the surface split is: **Sensei dispatches** (`image: <prompt>` → submits a job, prints `rendering, see Pupil [job <id>]`); **Pupil shows** (Image tab polls `/sdcpp/v1/jobs/<id>`, paints the inline PNG when done). sd-server stays bound to loopback; Pupil reaches it via `stt_server.py`'s `/sdcpp/*` reverse proxy on `:8080` (same-origin, no new port exposed, works over Tailscale because :8080 is already on `0.0.0.0`).

**How to apply:** When discussing image-gen UX, latency, or "should this go in Sensei or Pupil" — 56s is the anchor. Sub-features that are inherently >10s (video gen, batch image gen, upscale-then-render) should follow the same Sensei-dispatches/Pupil-shows pattern. Don't propose synchronous chat-inline rendering on this CPU. Levers to claw time back if needed: TAESD already on; Vulkan SDK upgrade (sudo handoff) for ~2× HD 530 lift; 2-step LCM at quality cost; lower resolution. Engine wrapper: `~/scripts/image_engine/imagegen.sh` (subcommands: health/submit/status/fetch/gen).
