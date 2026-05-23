---
name: feedback-no-daily-disk-nag
description: Operator does not want disk-space callouts in routine status updates. Mention only when actively breaking something.
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 05017a5d-eb68-4621-983a-d4f7e91b8fbd
---

Operator 2026-05-22 (late): "I just don't want you to put it up every day get on my nerves. It's gonna be about a month from now. I'm gonna get some money first."

**Rule:** Do NOT include disk usage in daily/health-check summaries unless usage is causing an actual failure (write errors, package install fails, Ollama can't pull). 79–90% is normal and not actionable for this operator's setup right now.

**Why:** They know about it, they have a flash drive standby and a money-driven plan to add storage in ~1 month. Repeated callouts are noise.

**How to apply:** In daily-check output, omit the disk row unless `df -h ~` shows ≥95% OR a recent operation has logged a write/disk-full error. When in doubt, leave it out.
