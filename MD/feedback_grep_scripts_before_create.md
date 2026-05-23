---
name: Before creating script-shaped tools, grep ~/scripts for an existing tool and run it if found
description: Before emitting CREATE for a script-shaped natural request such as matrix rain, *_rain.sh, demo builders, weather/image-gen demos, or any named local tool, Sensei must first RUN a grep/find audit across ~/scripts/*.sh and ~/scripts/*.py using the user's keywords. If a likely existing match is found, RUN that existing script instead of CREATE-ing a new Desktop copy. Only CREATE after the audit finds no match.
type: feedback
originSessionId: 019defe9-9233-7843-962d-8dbd742b032b
---
When Elijah asks for a script-shaped thing by natural language, Sensei must check the existing `~/scripts/` tool shelf before fabricating a new file.

**Learning-status note:** This is a hand-written feedback rule created by Codex/Claude and synced into Sensei memory. It is not evidence that Sensei autonomously learned from the matrix rain failure. Sensei can apply this guardrail after sync; the self-observation and harvest-to-memory learning loop is still unbuilt.

**Why:** 2026-05-03 natural-request test exposed the real gap. For "matrix rain", Sensei tried to `CREATE` a new `/home/elijah/Desktop/matrix_rain.sh` instead of discovering and running the good existing `~/scripts/matrix_rain.sh`. That creates two failures at once: it bypasses the maintained tool and it risks shadowing it with a lower-quality Desktop script. A later Groq attempt emitted `RUN: ~/scripts/matrix_rain.sh`, but only because Elijah's diagnostic prompt already contained that explicit path. That is not tool discovery.

**Hard rule:** Before any `CREATE:` for a script-like named tool, Sensei must run a discovery command first.

Good first moves:

```bash
grep -lEi "matrix|rain" ~/scripts/*.sh ~/scripts/*.py 2>/dev/null
grep -lEi "weather|demo" ~/scripts/*.sh ~/scripts/*.py 2>/dev/null
find ~/scripts -maxdepth 1 -type f \( -name "*.sh" -o -name "*.py" \) -iname "*matrix*" -print
```

**How to apply:**
- For requests containing script/tool words like `matrix`, `rain`, `demo`, `weather`, `image gen`, `generator`, `dashboard`, `viewer`, `player`, or an apparent filename such as `*_rain.sh`, audit `~/scripts/` first.
- If the audit returns a likely match, emit `RUN:` for that existing file. Prefer `bash ~/scripts/name.sh` for shell scripts unless direct execution is known to work.
- If multiple matches are plausible, read/list the top matches and choose the closest obvious one. Ask only if there is real ambiguity.
- If no match exists, then `CREATE:` is allowed.
- Do not create a same-named Desktop script until the audit has failed. Desktop copies can shadow or dilute the good project tool.
- This is not a matrix-only rule. It applies to every natural request where the user might be naming a tool that already lives in `~/scripts/`.
