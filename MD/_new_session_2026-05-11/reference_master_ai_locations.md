---
name: reference-master-ai-locations
description: "Where master-ai / Sensei artifacts live on disk — code, runtime data, baselines, and the small Bash router"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 9ee8a8f7-ee27-4ebe-9264-c84ee3dac19a
---

Master-ai / Sensei lives across several locations on this machine. Use these when the user references "master-ai", "Sensei", "the cleanup tool", or "my AI router".

## Code
- `~/scripts/sensei_clean/` — Python package (the intended trusted core; see [[project-sensei]])
- `~/scripts/sensei_clean.py` — CLI wrapper
- `~/scripts/test_sensei_clean.py` — stdlib unittest suite
- `~/Downloads/drive_cleanup.sh` — legacy review-first Bash Drive cleanup (informed the Python design)
- `~/ai-master.sh` — tiny Ollama prompt wrapper, picks model and posts to `localhost:11434`
- `~/ai-router.sh` — even simpler Ollama caller
- `~/master-ai/` — currently only contains a copy of `ai-master.sh`; not a git repo

## Runtime / data (dotdirs in $HOME)
- `~/.master_ai_profiles/`
- `~/.master_ai_chats/`
- `~/.master_ai_briefings/`
- `~/.master_ai_update_backups/` — dated snapshots, e.g. `20260428_192026/`, `20260429_191717/`

## Baselines and benchmarks
- `~/Desktop/master-ai-baseline-2026-05-11/`
- `~/Desktop/master-ai-baseline-20260511_103629/`
- `~/Desktop/master_ai_benchmark/`
- `~/Desktop/master_ai_cleanups/`
- `~/Desktop/AI_CONTEXT/`
- `~/Desktop/_cleanup_review_2026-04-25/master_ai_benchmark/` (archived)

## GitHub
- Only public repo for user `ebey317` is `ebey317/ebey317` (profile README). No master-ai/sensei repo on GitHub yet.
