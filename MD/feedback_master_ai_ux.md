---
name: Master AI UX Preferences
description: How Elijah wants the Master AI system to behave — confirmed patterns and things to avoid
type: feedback
originSessionId: 4a207176-3e6e-4bf9-8a27-cccb78510ab3
---
**No redundant prompts after menu options.**
**Why:** "Back to menu? (y/n)" was pointless — y just goes back, x is the real exit anyway.
**How to apply:** master.sh auto-returns to menu after every option. x is the only exit.

---

**Option 4 = master_ai.py (terminal AI + opens UI). Option 14 = pc_control.sh. One instance of each — never duplicate.**
**Why:** User had multiple windows opening and wanted exactly 2: AI terminal + browser UI.
**How to apply:** Don't add pc_control.sh to option 4 or master_ai.py to option 14.

---

**master_ai.py input: type text and press Enter to send. Voice = explicit 'v' command only.**
**Why:** Empty Enter was accidentally triggering 5-second voice recording. User hated the 't ' prefix too.
**How to apply:** Any unrecognized input = direct message to AI. Voice only on 'v' or 'r'.

---

**Arrow keys must work in master_ai.py. Always import readline.**
**Why:** Without it, arrow keys printed ^[[C escape codes instead of moving the cursor.
**How to apply:** readline is imported at top of master_ai.py with history file ~/.master_ai_history.

---

**pc_control.sh keyboard feel is preferred for the terminal experience.**
**Why:** bash read -e gives readline, pre-filled edit prompts, arrow key history. User specifically said option 14's keyboard setup was better.
**How to apply:** Keep pc_control.sh intact. master_ai.py now has readline too.
