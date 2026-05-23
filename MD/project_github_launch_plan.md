---
name: GitHub launch plan for Master AI — 2026-04-23 recommendations
description: Saved-for-later reference. Concrete GitHub resources and next steps for getting Master AI from "zero repos published" to "pitch-ready on ebey317's profile." Six specific recommendations, ranked by leverage. Written 2026-04-23 after lock-in session.
type: project
originSessionId: 4e17fa5e-95c3-4e24-8a99-1f0bd42a1787
---
## Context

Elijah is logged in to GitHub as `ebey317` with **zero repos published** as of 2026-04-23. He has rich local commit history in `~/scripts`. Publishing Master AI (or a stripped build) is a known forward move — see `project_sale_readiness.md` and the "pack it up for sale" ritual.

This file is the reference list of GitHub resources + next steps. Open this when Elijah is ready to start the GitHub publish / profile phase.

## Platform decision — 2026-04-26

**GitHub is the public surface. Gumroad is the storefront. GitLab is OFF the path.**

Elijah briefly opened a GitLab project (`ebey317-group/ebey317-project`) on 2026-04-26 and closed it the same day after we walked through the choice. Reasoning:
- Buyers + maker-tool discovery audience defaults to GitHub (`r/LocalLLaMA`, HN, awesome-selfhosted).
- Gumroad handles the $100 sale, file delivery, and tax — neither GitHub nor GitLab does payment processing.
- Two platforms is enough; three fragments the brand.

**Going forward:** do not suggest GitLab as a hosting / publishing / sale path. GitHub + Gumroad only. If Elijah wants private code hosting separately, GitHub Private repos cover that — no second platform needed.

## Gumroad status — 2026-04-26

- **Live URL:** `https://ebey.gumroad.com/` (handle: `ebey`, claimed 2026-04-26)
- **Display name:** "Elijah"
- **State as of claim:** skeleton — empty bio, no profile photo, no products, no social links
- **Copy ready to paste:** `~/Documents/templates/personal_brand_kit/markdown/gumroad_master_ai_listing.md` has the Master AI product listing + a Creator Profile section with the 150-word bio + photo specs + link guidance
- **Next concrete step for Elijah:** paste bio into Settings → Profile, claim social links, add product when installer is packed-for-sale
- **Cross-references for future bio/listing edits:** when bio copy changes anywhere (brand kit HTML, ODT, Gumroad listings), update all four — they MUST stay in sync because buyers cross-check.

## The six recommendations (ranked by leverage)

1. **Profile README = launchpad.** `abhisheknaiidu/awesome-github-profile-readme` — 600+ curated profile READMEs to steal patterns from. The `ebey317/ebey317` special repo renders on your profile home. That's where the locked-verbatim pitch belongs:
   > "Master AI is for anyone who has ever asked ChatGPT Claude or Google Gemini a question and it responded paste this in your terminal."
   Draft, customize, push. ~1 hour to pitch-ready. **First GitHub move to make.**

2. **Ollama Discussions for Modelfile + cold-load tricks.** `ollama/ollama` → Discussions tab. Most-upvoted threads cover KV cache persistence, `keep_alive` tuning, and Modelfile SYSTEM optimization — directly relevant to the 80s cold-load tax we measured on 2026-04-23 and documented in `project_locked_model_set.md`. 10-min skim, no publish required.

3. **Pre-commit hooks for drift prevention.** `pre-commit/pre-commit` — git hook framework. Given we caught a stale reason-string at `master_ai.py:1031` today (router reason referenced `qwen2.5-coder:7b` while actually dispatching to `master-ai`), a tiny custom hook could grep for reason-strings that reference models not present in `MODELS` dict values and fail the commit. Prevents today's bug class from recurring silently. ~30 min to wire. Requires repo to exist.

4. **Aider's README as the pitch model.** `Aider-AI/aider` — same pain-first framing Master AI uses. Study structure: headline → 3-line what-it-does → demo gif → install → community. When writing Master AI's repo README, this is the shape to copy.

5. **Distribution channels (GitHub-adjacent, not GitHub itself).**
   - `r/LocalLLaMA` — the target audience
   - HackerNews "Show HN" — the launch channel
   - `awesome-selfhosted` (GitHub repo) — a PR adding Master AI to its "AI Assistants" section is a free directory-listing for every self-hoster browsing the list
   These activate AFTER the repo exists. Not useful before.

6. **SKIP until tests exist: GitHub Actions / CI.** Wiring CI for a codebase without tests is theater. Wire it when the first real test suite lands — not before. Don't waste effort here yet.

## Next step when Elijah opens this phase

Single action: draft the `ebey317/ebey317` profile README using:
- The locked verbatim pitch (from `project_pitch_copy_paste_terminal.md`)
- The product names (Master AI = brand; Sensei + Pupil = apps)
- A Sensei screenshot + a Pupil screenshot (demo-worthy)
- The "genius right next to you" frame (from `project_pitch_genius_beside_you.md`)
- A short "building in public" line linking back to future repos

Draft locally first, review, then push. No risk of exposing keys because profile README never references `.master_ai_keys` or personal data.

## Related memory

- `project_pitch_copy_paste_terminal.md` — the verbatim pitch line (locked, do not paraphrase).
- `project_pitch_genius_beside_you.md` — complementary "what it is" pitch.
- `project_sale_readiness.md` — $100 A+ target + wedge positioning.
- `feedback_pack_it_up_for_sale.md` — pre-publish strip ritual (run before any repo push that includes code from `~/scripts/`).
- `project_locked_model_set.md` — the model state that should be reflected in any README's "what's under the hood" section.
- `user_builder_vs_distributor.md` — distribution is a muscle Elijah hasn't flexed yet; GitHub publish is the opening rep.
