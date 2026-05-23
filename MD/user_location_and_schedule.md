---
name: Elijah's Location, Timezone, and Work Schedule
description: Originally from Muncie, IN. Currently based in Indianapolis, Indiana — America/Indiana/Indianapolis timezone (EDT in summer, EST in winter). Leaves home for work ~5:15 AM EDT. Needs remote access to madam-mary/Sensei all workday. References "EST" colloquially year-round but system is on DST in summer.
type: user
originSessionId: c53f933b-955f-4b1d-8b2b-dc75f15b261b
---
**Originally from:** Muncie, Indiana (confirmed 2026-04-26). Use this for biographical / origin-story copy ("Muncie kid"). Don't replace the Indianapolis present-tense base — both are true.

**Where (current):** Indianapolis, Indiana.

**Timezone:** `America/Indiana/Indianapolis` — UTC-5 in winter (EST), UTC-4 in summer (EDT). Indianapolis DOES observe DST (since 2006). Elijah sometimes says "EST" colloquially when he means "eastern time" — don't correct him unless the hour matters for a scheduled task.

**Workday pattern (confirmed 2026-04-21):**
- Leaves home around 5:15 AM local
- At work all day
- Needs Sensei / Master AI remotely accessible during work hours via Tailscale
- Home box `madam-mary` stays online 24/7 — see `project_sensei_always_on.md`

**Why this matters:**
- When Elijah references a time, default-interpret as Indianapolis local unless he says otherwise.
- When scheduling anything (cron, reminders, maintenance windows), use his local time.
- When he asks "am I connecting from work okay" — the answer depends on the laptop/phone on his tailnet, not on madam-mary.
- On DST boundaries (March / November) his colloquial "EST" reference will be off by an hour from reality — system time is always correct, but copy in responses should use EDT when appropriate.

**How to apply:**
- Include timezone context (EDT/EST) when showing times in chat.
- If he asks for "current time" while at work, it's the same TZ as home — not his work location's TZ (unless work is in a different zone, which he hasn't mentioned).
- Scheduled remote-agents / cron jobs → use `America/Indiana/Indianapolis` explicitly in the crontab or schedule config, not UTC with manual offset.
