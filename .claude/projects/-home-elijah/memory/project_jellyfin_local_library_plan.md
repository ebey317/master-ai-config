---
name: project_jellyfin_local_library_plan
description: "Jellyfin disabled until ~1TB storage acquired; user owns IPTV content on remote servers, plans local library"
metadata: 
  node_type: memory
  type: project
  originSessionId: de36b780-0890-4b00-bda6-2ad92f2dec80
---

## Jellyfin — Disabled, Future Use

Jellyfin is installed but **disabled** (stopped + `systemctl disable jellyfin`).

**Why disabled:** No local storage yet. User owns IPTV content digitally on remote servers — too large to store all of it. Plan is to acquire ~1TB drive, selectively download content, and build a local library.

**When to re-enable:** Once 1TB storage is in place.
```bash
sudo systemctl enable --now jellyfin
# then point library path at the new drive
```

**Hypnotix** is the active IPTV player in the meantime — live channels from the subscription.

[[feedback_check_system_before_open]]
