# BioVega Consolidation + Field Manual Rebuild

## Context

**Two problems being solved in one plan:**

1. **The immediate problem.** `DAHKgK7YGSg` ("BioVega Field Manual") is filled with supplements/clean-nutrition copy (pea protein, ashwagandha, Daily Greens). I hallucinated that subject matter from a stale compaction summary. The actual BioVega — confirmed by the user's blueprint at Drive `1izprDD-JFhtfo5Y_CiMC1X-Km-p2ueTX23X1EwSk9h8` — is an off-grid sustainability field manual series: Biodiesel, Biogas, Wind/Electric, Materials/Foundations, Herbal Medicine.

2. **The root cause.** BioVega knowledge is scattered across ~15 surfaces: Drive docs, Drive PDFs, multiple Canva designs (183p, 61p, 14p, 11p, 1p), Google Photos. No canonical source. Compaction summaries drift, agents can't deep-recall, and the user has been re-explaining the brand to me. The user's directive: consolidate everything into ONE Google Doc, verify, then full clean sweep of duplicates across Drive + Canva (photos untouched, Field Manual design survives).

The consolidation MUST happen first. Otherwise we just produce another BioVega artifact on top of the same mess.

---

## Verification doctrine (operator's directive: "verify before deletion")

Deletion is irreversible across Drive and Canva. The plan treats verification as a hard, multi-pass gate, NOT a checkbox:

1. **Read-back verification.** After writing the canonical Doc, read it back and diff against every source. Flag anything missing.
2. **Spot-check verification.** Pick 5 random paragraphs from each source PDF/Doc, search the canonical Doc for them — must find each verbatim or trace to a documented edit.
3. **Operator review.** Send the operator the canonical Doc URL + a side-by-side index of "Sources In → Canonical Out" coverage. Operator reads, approves, or flags gaps.
4. **Dry-run deletion list.** Print the exact list of file IDs to be deleted (Drive + Canva) WITHOUT calling delete. Operator confirms the list.
5. **Single confirmation per surface.** Operator says "delete Drive" → Drive sweep runs. Operator says "delete Canva" → Canva sweep runs. No combined "yes."

If ANY step fails or operator hesitates → stop. Re-consolidate, don't delete.

## Phase 0 — Consolidation (must run before anything else)

### 0.1 Inventory every BioVega source

**Drive** (from earlier search):
- `1izprDD-JFhtfo5Y_CiMC1X-Km-p2ueTX23X1EwSk9h8` — Canva Bio-Vega Bueprint (Doc) — already read, style guide + structural spec
- `1gIh81dPDTI8qk3oEpOkIxKsFnLSVpl21rljvi2rWC8A` — BioVega MANIFESTO (Doc, 3.9MB)
- `123ur6gIqagNtA8p5M6q6kCF6ZYK9sGx2sQqBqBSxXrc` — Biovega full text (Doc, 196KB)
- `1jMeLib6WfyD8_iqe4br9ZK1L3T3FrtHly2x_pBVzgjw` — BioVega remastered (Doc, 102KB)
- `1yvnfM49r3XL_oGk8f1RyV1ssXLE6dXZ5MhBf8jTu63o` — BioVega remastered 2 (Doc, 101KB)
- `1Rs_GLD69H68MprCqKZfZXBRqwT7C1wlP` — BIOVEGA_AllIn_FINAL_v2.pdf (21MB)
- `1EbftBsLuze3kpVGAp9Xj534TCbsy6ruX` — BIOVEGA_Master_FINAL_v2.pdf (11MB)
- `1o6zIdf2wARGSpd5KwVFV927v4ITaOgUH` — BIOVEGA_ALL_IN_FINAL_v2.pdf (21MB, likely duplicate)
- `1X2s80UzTnA5h9c7JBRS5UI2aL9I5RdxQ` — Copy of BIOVEGA_All_In_One.pdf (21MB)
- `1sj1rtG1o6UQpU5sRDXKIAUyvHomLYZxu` — 10_BIOVEGA_Materials_RUBBER_and_ELASTOMERS.pdf (638KB)
- *(may be more on next page of search — paginate first)*

**Canva designs** (text + embedded illustrations):
- `DAG97kFw014` — BioVega remastered.pdf, 183 pages
- `DAG97xyQMEg` — BioVega remastered.pdf (Document), 183 pages, same name, likely duplicate
- `DAG9MAfXgjg` — BIOVEGA — INTEGRATED FIELD MANUAL, 1 page (cover treatment)
- `DAG9EEODWTI` — Copy of BIOVEGA_All_In_One.pdf, 61 pages
- `DAG9ogqgZz0` — BIOENGINEERING_MASTER_FINAL.pdf, 14 pages
- `DAG9lTaW1vg` — Copy of BIOVEGA_Fuel_and_Gas_VOL1_EXACT_WITH_NOTES_END.pdf, 11 pages
- `DAG97cqAbOw` — Gumroad link test.pdf, 3 pages (BioVega-adjacent)
- `DAG9Dxa_k7o` / `DAG89rjiE7A` — Master.pdf (31p / 32p, may be earlier BioVega master)
- Folder `FAF89tt3q4E` — empty wrapper

**Google Photos:** 16 BioVega images (via `sensei photos_search`).

### 0.2 Read all text content

For each Drive Doc and PDF: `read_file_content` (mime-type aware). For each Canva design: `get-design-content` for text + `start-editing-transaction` to inventory image asset IDs and `get-assets` to fetch thumbnails.

### 0.3 Assemble the unified Google Doc

**Target:** create new Doc in Drive (parent folder = `1COTP7Bq178T3pm9J_E6lz95aWmBwvcut` — the existing BioVega Drive folder where most BioVega Docs already live, so it stays in context).

**Title:** `BIOVEGA — Canonical Source (Master)`

**Structure** (follows blueprint's master architecture):
```
# BIOVEGA — Canonical Source (Master)
Edition v1 — consolidated [date]

## Part 0 — Identity & Doctrine
- Mission ("BioVega is not the shelter…")
- Field Manual Style Guide (from Blueprint doc, verbatim)
- Visual language: color codes, typography, icons, "STOP IF" logic
- Voice: command-driven, sensory/tactile

## Part 1 — Biodiesel Field Manual (A+)
[Full content merged from Drive PDFs + Canva text + BioVega remastered]

## Part 2 — Biogas Field Manual (A+)
[Full content + Y-Method diagram references]

## Part 3 — Wind & Electric Field Manual (A+)
[Power Spine + all field pages]

## Part 4 — Materials & Foundations (A+)
[All 10 material pamphlets: Aluminum, Steel, Ceramic, Cloth, Copper, Plastics, Glass, Wood, Rubber, Fasteners]

## Part 5 — Herbal Medicine Field Manual (A+)
[All 16 herbs + preparation appendices]

## Appendix A — Manifesto
[Full content from MANIFESTO Doc]

## Appendix B — Visual Asset Inventory
[Table: Canva asset_id → description → which section uses it]
[Google Photos URL list with captions]

## Appendix C — Provenance Log
[For every paragraph: which source file/page it came from. Critical for the verification step.]
```

### 0.4 Verification (gate before any deletion)

The deletion step is irreversible. Don't proceed until ALL of these pass:

1. **Word-count check.** Sum word counts of every source → compare against the unified Doc. Allow ≤5% loss (formatting/duplication trimming). Anything more, stop and investigate.
2. **Key-phrase check.** Grep the unified Doc for canonical phrases that MUST be present:
   - "STOP IF" — appears in every section
   - "BIO-VEGA — LIVING SUSTAINABLY" — header from style guide
   - "BioVega is not the shelter" — manifesto thesis
   - Each of: "Biodiesel", "Biogas", "Wind", "Materials", "Herbal" — five-section coverage
   - Each of 16 herbs (Yarrow, Plantain, Calendula, Comfrey, Arnica, Garlic, Ginger, Peppermint, Willow Bark, Turmeric, Mullein, Pine Needles, Chamomile, Lemon Balm, Valerian, Clove)
   - Each of 10 materials (Aluminum, Steel & Iron, Ceramic, Cloth, Copper & Brass, Plastics, Glass, Wood, Rubber, Fasteners)
3. **Asset inventory check.** Every Canva `MA*` asset_id from source designs must appear in Appendix B.
4. **Sensory-cue spot check.** The user's reference biodiesel section (with "place your hand on the side for one second" / "hover your face over the oil for 10 seconds" tactile cues) must be present verbatim — it's the voice exemplar.
5. **Human gate.** Present the unified Doc's URL + a one-page summary diff to user. User signs off explicitly before deletion runs.

### 0.5 Asset preservation (BEFORE Canva sweep)

Canva designs hold the hand-painted illustrations. If a design gets deleted, its asset_ids may become orphaned. So:

1. Walk every Canva BioVega design, extract every unique image asset_id
2. `get-assets` to grab the thumbnail URLs
3. `upload-asset-from-url` to re-upload each illustration as a standalone uploaded asset (lives in user's account, design-independent)
4. Record the new standalone asset_ids in the Doc's Appendix B
5. ONLY after every illustration is safely re-uploaded → proceed to deletion

### 0.6 Full clean sweep (only after 0.4 and 0.5 pass)

**Drive deletions:**
- Every BioVega Doc and PDF in the inventory list EXCEPT the new canonical Doc
- Includes: Blueprint, MANIFESTO, full text, remastered (×2), AllIn_FINAL (×2), Master_FINAL, Copy of All_In_One, Materials_RUBBER pamphlet, plus any others found in 0.1 pagination

**Canva deletions:**
- Every BioVega design in the inventory list EXCEPT `DAHKgK7YGSg` (Field Manual — the active output we'll rebuild in Phase 2)
- Includes: remastered (×2), INTEGRATED FIELD MANUAL, All_In_One, BIOENGINEERING_MASTER_FINAL, Fuel_and_Gas_VOL1, Gumroad link test, both Master.pdfs (if confirmed BioVega)
- Folder `FAF89tt3q4E` itself can stay (it's the holding pen for the new uploaded standalone assets)

**Photos:** untouched.

---

## Phase 1 — Memory upgrades (after consolidation)

Three files in `/home/elijah/.claude/projects/-home-elijah/memory/`:

### `project_biovega_real_identity.md`
```
BioVega = sustainability / off-grid field manual series. NOT a supplement brand.
Sections: Biodiesel, Biogas, Wind & Electric, Materials & Foundations, Herbal Medicine.
Voice: command-driven, sensory/tactile, "STOP IF" safety logic.
Visual identity: hand-painted illustrations, "BIO-VEGA — LIVING SUSTAINABLY" header.

Canonical source (single file, no other copies exist):
  Drive Doc: [new doc ID from Phase 0.3]
  Parent folder: 1COTP7Bq178T3pm9J_E6lz95aWmBwvcut

Visual assets: standalone uploads in user's Canva account (see canonical Doc, Appendix B for asset_id list).
Photos: 16 in Google Photos, searchable via sensei photos_search "BioVega".

If you find ANY other "BioVega" file in Drive or Canva — it's wrong, it shouldn't exist post-sweep. Report it.
```

### `reference_elijah_asset_index.md`
```
The "deep recall" index. When the operator says "I have it" — search connectors before asking.

Authenticated connectors (already wired, do NOT prompt for accounts, do NOT WebFetch):
  Canva (designs, folders, brand templates) — via Canva MCP search-designs / search-folders
  Google Drive — via Drive MCP search_files (supports fulltext + title)
  Google Photos — via sensei photos_search (WebFetch 302s to Google login, won't work)
  Gmail — via Gmail MCP
  Calendar — via Calendar MCP
  Todoist / Indeed / ZipRecruiter — via their MCPs
  Hugging Face Z-Image — for generating new assets if existing don't fit

Search-first rule: when operator mentions an asset, project, or "I have it/uploaded it,"
issue parallel searches across Canva + Drive + Photos BEFORE asking where it is.

Known canonical sources:
  BioVega — see [[project_biovega_real_identity]]
  [Future: other consolidated brands/projects link here]
```

### `feedback_canva_image_swap_before_text.md`
```
On a template-based Canva design built around photography (Field Manual / Pitch Deck),
the IMAGES carry the brand. Text-only swaps produce parody — "BIOVEGA" floating over
tactical-gear soldier photos is the same template with new labels.

Rule: source/upload images FIRST, then write text last. Use start-editing-transaction
to inventory editable image fills (editable:true), get-assets to see thumbnails,
update_fill operations to swap each one BEFORE any replace_text.

Why: I burned a session producing 141 successful text ops that committed to a still-
military design. Operator had to point out the images. (2026-05-23)
```

---

## Phase 2 — Rebuild `DAHKgK7YGSg` from the canonical source

### 2.1 Page plan (15 pages mapped to blueprint structure)

| Pg | Section | Content |
|----|---------|---------|
| 1  | Cover | "BIO-VEGA — LIVING SUSTAINABLY / Integrated Field Manual" + illustrated tent scene |
| 2  | Front Matter | Field Doctrine / How To Use This Manual (STOP IF, color codes, icon legend) |
| 3  | Mission | "BioVega is not the shelter — it's what allows the shelter to exist" |
| 4  | Section I cards | Biodiesel: gather / heat / combine (3 numbered cards) |
| 5  | Section I table | Biodiesel materials + ratios + temps |
| 6  | Section II cards | Biogas: digester / gas bag / heat coil / burner (4 cards) |
| 7  | Section II protocol | Build sequence with sensory checks |
| 8  | Section III intro | Wind & Electric power spine diagram |
| 9  | Section III protocol | Daily deployment: safety reset / motor / blade / mast |
| 10 | Section IV intro | Materials & Foundations — who/what we build for |
| 11 | Section IV table | Material allocation: Aluminum, Steel, Ceramic, Copper, Rubber |
| 12 | Standards | STOP IF / PASS / Field Safe / Cross-Reference (4 callouts) |
| 13 | The Enemy | Waste, fossil fuel dependency, deforestation, off-grid risk |
| 14 | Section V | Herbal Medicine — Enlist Now (16 herbs reference) |
| 15 | Index | Bio-Vega Index, QR cross-references, contact, edition |

All text pulled FROM the canonical Doc — no invention.

### 2.2 Execution

1. `start-editing-transaction` on `DAHKgK7YGSg`
2. **Batch A:** `perform-editing-operations` — 14 `update_fill` ops swapping military photos for standalone BioVega illustration assets (from Phase 0.5 re-uploads)
3. **Batch B:** `perform-editing-operations` — all `replace_text` ops with REAL content from canonical Doc per page plan above
4. **Pause + show user** page-1 thumbnail before commit (no more silent commits — user reviews first)
5. `commit-editing-transaction` only after user approval

---

## Verification (end-to-end)

- `get-design-content` on `DAHKgK7YGSg` pages 1, 6, 11, 14 → text contains "BIO-VEGA", "BIOGAS", "Materials", "Herbal" — NOT "Pea Protein" or "Daily Greens"
- `get-design-pages` fresh thumbnail page 1 → hand-painted tent illustration, no military soldiers
- Drive search for "BioVega" → returns exactly ONE file (the canonical Doc)
- Canva search for "BioVega" → returns exactly ONE design (`DAHKgK7YGSg`, the Field Manual)
- Memory: opening a fresh session and asking "what is BioVega" returns "sustainability / off-grid field manual" without any guessing

---

## Hard rules (will not be broken this session)

- **No deletion** until Phase 0.4 verification AND user explicit sign-off
- **No commit** on `DAHKgK7YGSg` until user sees page-1 thumbnail and approves
- **No invented content** — every word in the Field Manual must trace back to the canonical Doc
- **No memory writes** before Phase 0 completes — premature memory codifies the wrong state

---

## Out of scope this plan

- Generating new images via Z-Image (user has existing illustrations; reuse them)
- Touching Google Photos (operator excluded them)
- Building other BioVega outputs (laminated cards, ring binders, etc. from the blueprint) — that's a future plan once the canonical Doc exists
