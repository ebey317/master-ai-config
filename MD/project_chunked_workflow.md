---
name: Chunked Agentic Workflow — Sensei orchestrates, local model executes
description: The single most important feature for apocalypse-mode local AI. Break big tasks into chunks the local model can handle, save each to file, merge at the end. Sensei is the orchestrator, the AI fills each chunk. Turns a 7B-30B local model into a 15,000-word producer without needing cloud.
type: project
originSessionId: 7c65435d-cf08-49b9-a9b6-749411398346
---
## Elijah's insight (2026-04-19)

> "Maybe not 15,000 words in one bash but 15,000 words and three bashes — like we save the files and then we upload them all separately and then renders in the end... We put all of that into Sensei and it knows what to do."

He just articulated **map-reduce for local AI** — the single most important feature to make apocalypse-mode viable on CPU hardware.

## What it is

Instead of asking a local 7B-30B model to produce 15,000 coherent words in one shot (which it can't — context, attention, and time all collapse), Sensei:

1. **Plans** the task into N chunks (outline first, N ≈ target_words / 3,000)
2. **Executes** chunk-by-chunk — each chunk is a self-contained sub-task the local model CAN handle (3,000-5,000 words or ~50-100 lines of code)
3. **Persists** each chunk to disk immediately (crash-safe, resumable — if the session dies at chunk 7 of 10, chunks 1-7 are saved)
4. **Stitches** at the end with a final pass — either a merge-and-polish run through the local model OR just concatenation with a transition pass
5. **Reports** progress so the user knows how far along the 6-hour session is

The USER describes the big thing. SENSEI does the chunking + orchestration + merge. THE MODEL only ever sees a chunk-sized task.

## Why this is the apocalypse-mode unlock

On a 32-GB-RAM CPU-only machine running `qwen3:30b-a3b`:
- One 5,000-word chunk → ~2-5 minutes to generate
- 10 chunks → ~30-50 minutes for the body
- 1 merge pass → ~10 minutes
- **Total: ~45-60 minutes for a 50,000-word document or 1,500-line multi-file app**

That's real. That's what Elijah is after. That's what breaks the "local AI is a toy" ceiling.

Without chunked workflow: local models can write ~2,000 useful words before drift, then they wander. WITH chunked workflow: the drift is bounded per chunk, then reset, so total output quality stays coherent.

## The real use cases (NOT just apps)

Elijah's own examples when he described this:
- **"A power plant and we might need to do some stuff then"** — operating manuals, troubleshooting guides, restart procedures for industrial systems
- Survival engineering from scrap materials (tied to scrap scanner)
- Plant-based medicine workflows (tied to apothecary scanner)
- Rebuilding knowledge for post-collapse tech (Off-Grid Civilization Information corpus queries)
- Long-form documents — 50-page manuals, codebases, design documents

The output is **reference + reasoning + document-generation**, not interactive chat. Which is EXACTLY what chunked workflow is good at.

## Implementation sketch

A new Sensei mode + helper:

### `chunked: <big task>` command
```
sensei> chunked: write a 10,000 word manual for restarting a coal power plant after a week offline
🥷 Planning...
🥷 Task broken into 8 chunks. ETA: ~45 min on qwen3:30b-a3b.
🥷 Chunks will save to ~/.sensei_chunks/<task_id>/
🥷 Type 'go' to begin, 'plan' to see the outline, 'cancel' to abort.
```

### `~/.sensei_chunks/<task_id>/` layout
```
.
├── plan.json           # full outline + chunk specs + progress state
├── chunk_01.md         # generated, done
├── chunk_02.md         # generated, done
├── chunk_03.md         # empty — in progress
├── .../
└── final.md            # merged output (written at end)
```

### Resumability
If Sensei crashes or Elijah walks away, `plan.json` tracks which chunks are done. On restart, Sensei says *"Resuming chunk 3 of 8..."* — no lost work.

### Chunk prompt template (per chunk)
```
[TASK CONTEXT]
Full task: {original_user_request}

[OUTLINE]
{full plan with all chunk headings}

[PREVIOUS CHUNKS SUMMARY]
{1-paragraph summary of each completed chunk so far}

[YOUR CHUNK]
Chunk {N} of {TOTAL}: {this_chunk_heading}
Target length: {words} words.

Write ONLY this chunk. Don't repeat previous chunks. Don't preview later ones.
End with a single-sentence wrap so the next chunk picks up cleanly.
```

Each model sees a chunk-sized request. Quality stays high. Coherence is maintained by the outline + summary rails.

### Merge pass
After all chunks are written, one final pass:
- Read `chunk_01.md` through `chunk_N.md`
- Ask the model: *"Smooth the transitions. Fix any contradictions between chunks. Output the final unified document."*
- Write to `final.md`

On a slow CPU model, this pass is the expensive part — maybe 15-30 minutes. But it's one pass, not per-chunk.

## How this interacts with other Master AI pieces

- **Dojo gate + PROJECTS.md:** a chunked task is a PROJECTS.md task. Sensei marks it `[x]` when the whole thing is done. Each chunk isn't a separate task — it's a progress tick within one task.
- **24/7 always-on design:** chunks can run while Elijah sleeps. Fire a 10k-word task, walk away, come back 2 hours later, it's done. This is the "genius next to you" pitch actually working hands-free on local hardware.
- **Apocalypse mode:** this is THE feature that makes apocalypse viable. Without chunking, local AI is limited to quick Q&A. With chunking, local AI can produce manuals, documents, codebases.
- **Voice-to-text + TTS:** Elijah describes the task verbally, Pupil reads back each chunk as it's produced, he course-corrects between chunks. True hands-free long-form work.

## How to apply

- Treat chunked workflow as a **core apocalypse-mode feature**, not a nice-to-have. When recommending hardware/model choices, evaluate them through this lens: *"will they support chunked workflow well?"*
  - `qwen3:30b-a3b` — YES, ideal (MoE = fast per chunk)
  - `qwen2.5-coder:32b` — YES for code-heavy chunks (slower but high quality)
  - `qwen2.5:7b` — YES for lighter chunks (sacrifices some coherence but fast)
  - `llama3.3:70b-q2` — questionable, too slow per chunk

- When the user says "I want to build X" and X is substantial (>3,000 words / >200 lines / >5 files), default to suggesting chunked mode instead of a single-shot request.

- Build the chunker itself as HARDCODED Python/bash — not AI. The AI fills each chunk; the scaffolding is deterministic. Matches the "70% hardcoded, 30% AI illusion" principle. Scaffolding works even when the model is dumb.
