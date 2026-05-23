# Sensei branch diff — regression-suspect audit

Branch: `clone/parity-rebuild` (HEAD `a079bd4`, ahead of master)
Audit window: 6 commits flagged as regression suspects for Indeed navigation on the local qwen2.5:7b lane.

---

## 86ccb59 — `fix(master_ai): add url_grounding module and skip deterministic routes on chrome turns`

**Files:** `master_ai.py` (+15/-12), `url_grounding.py` (new, +90)
**What changed:** Added a new `url_grounding.py` module. In `master_ai.py`, the existing deterministic-route short-circuits (the small set of hard-coded keyword→action shortcuts) now skip when the turn is a `chrome_extension` turn. The reasoning was that chrome turns need full model reasoning, not the short-circuit fallback.
**Why it could break local-7B Indeed nav:** Before this commit, certain natural-language prompts ("go to Indeed", "open ZipRecruiter") had a deterministic path that emitted `BROWSER_NAV:` without round-tripping through the model. After 86ccb59, chrome turns rely entirely on the model's own emission discipline. Local 7B is the weaker emitter — when the deterministic path was removed for chrome turns, local 7B lost a safety net that cloud lanes don't need. **Regression vector: MEDIUM.** Worth a guarded re-enable for known navigation phrases when the lane is local-7B.

---

## a011cae — `fix(cloud_system): directive-first OUTPUT CONTRACT to stop prose-only replies`

**Files:** `master_ai.py` (+32/-7)
**What changed:** Replaced the "REASON BEFORE EMITTING" rule with an OUTPUT CONTRACT (DIRECTIVE-FIRST). New rule: when the request implies action, the FIRST LINE of the reply MUST be a directive token at column 0. Annotation is optional and follows. Includes explicit INVALID/VALID examples.
**Scope flag in commit body:** `CLOUD_SYSTEM only. LOCAL_DIRECTIVE_HINT mirror deferred.`
**Why it could break local-7B Indeed nav:** The local 7B Modelfile retained the older "REASON BEFORE EMITTING" rule — which is half-satisfiable (model emits the reason, drops the directive). Local 7B is the lane most likely to fail half-satisfiable rules. **Regression vector: HIGH.** This is the exact gap the parity rebuild's pattern (a) Output Contract single-source file is designed to close. Mirror to Modelfile-master-ai is overdue and a hard prerequisite for local-lane nav reliability.

---

## 0a71d43 — `fix(cloud_system): teach JOB APPLICATION INTENT → RUN_SKILL: apply-job-session`

**Files:** `master_ai.py` (CLOUD_SYSTEM string)
**What changed:** New teaching block in CLOUD_SYSTEM mapping apply-jobs natural language → `RUN_SKILL: apply-job-session`. Includes URL construction from keywords+location, the "DO NOT emit BROWSER_* directly" rule, and a worked example. Bridge already exists at `master_ai.py:10712`.
**Scope flag:** CLOUD_SYSTEM only at the time of 0a71d43.
**Why it could break local-7B Indeed nav (until today):** Local 7B never learned the apply→RUN_SKILL mapping. Apply-jobs prompts on local lane produced prose-only or wandering BROWSER_NAV chains that never converged. **Regression vector: RESOLVED 2026-05-17 20:18:52 in commit `a079bd4`** (Codex landed the Modelfile mirror this session). Verify: `grep "JOB APPLICATION INTENT" Modelfile-master-ai` and `ollama show master-ai --modelfile | grep "RUN_SKILL: apply-job-session"`.

---

## 555cc09 — `Short-circuit pure acknowledgments`

**Files:** `master_ai.py` (+38/-1)
**What changed:** Server-side short-circuit for pure-ack turns ("ok", "thanks", "got it", "nice"). Pairs with the side_panel.js Wave 1.1 ack pre-filter.
**Why it could break local-7B Indeed nav:** This is the single highest-risk commit in the audit window for the local lane. The plan flags it explicitly: "Combined with local 7B's known inability to emit `<PLAN>` block reliably, this pair will terminate every continuation round on the local lane." Mechanism: if the server-side classifier mis-IDs a continuation reply as a pure-ack (because local 7B emitted "got it, navigating now" instead of a clean `BROWSER_NAV: ...`), the short-circuit fires, the loop terminates, and the user sees "no actions" with no nav. **Regression vector: CRITICAL for local 7B.** Mitigation: tighten the ack classifier to require ZERO directive-shaped tokens in the reply before short-circuiting, OR gate the short-circuit on lane=cloud only. Cross-reference with [[feedback_no_synth_routes_as_skills]] — the ack short-circuit walks the line.

---

## 09293bb — `feat(server): add loop_fsm.py — server-authoritative loop termination FSM`

**Files:** `loop_fsm.py` (new, +260)
**What changed:** Six-state typed FSM (IDLE / PLANNING / EXECUTING / AWAITING_TOOL_RESULT / AWAITING_USER / DONE) with named transition table, `RefusedTransition` exception on illegal moves, and `wire_view()` collapse to client guard vocab. Structural property: `(DONE, CONTINUE)` not in `_TRANSITIONS`, so loop-pump continuations from terminal turns raise. Closes "Failure 1" (17-turn ack-after-done auto-fire) at FSM layer.
**Integration status per memory `project_session_end_2026_05_16_evening.md`:** "Deferred: O6 integration into `_m.handle()` retry path."
**Why it could break local-7B Indeed nav:** If the FSM is wired but the local 7B emits state-ambiguous replies (no clear DONE marker, no clear directive), `wire_view()` may resolve to terminal_reason that closes the loop prematurely. Also: AWAITING_USER is doing double-duty (hard-block AND soft-confirm) — see D bugging §2 / port pattern (b). **Regression vector: MEDIUM, pending integration status check.** Verify whether `_m.handle()` actually imports `loop_fsm` today: `grep "from loop_fsm\|import loop_fsm" master_ai.py stt_server.py`.

---

## db54f18 — `Mirror LOOP TERMINATION rule into CLOUD_SYSTEM`

**Files:** `master_ai.py` (+13/0)
**What changed:** Mirrored the LOOP TERMINATION rule from Modelfile-master-ai:52 into CLOUD_SYSTEM. Verbatim copy, placed between POST-ACTION CONFIRMATION and REASON BEFORE EMITTING. Driven by live test showing cloud-fast→Groq fired 17 separate turns on one prompt because Groq never saw the Modelfile-only rule.
**Why it could break local-7B Indeed nav:** It can't — this commit is a fix, not a regression. It brought cloud lanes into parity with local on termination. The lesson it locked in (per [[feedback_mirror_modelfile_into_cloud_system]]) is the discipline the parity rebuild now applies to every prompt-surface change. **Regression vector: NONE (corrective).** Status: in branch `clone/parity-rebuild`, in master, and in `apply-job-session` and `loop-fsm` branches per `git branch --contains db54f18 -a`.

---

## Summary — by regression severity for local 7B Indeed nav

| Severity | Commit | Mitigation status |
|---|---|---|
| **CRITICAL** | 555cc09 — server-side ack short-circuit | Open. Needs ack classifier to require zero directive tokens, or lane=cloud gate. |
| **HIGH** | a011cae — directive-first OUTPUT CONTRACT, CLOUD_SYSTEM only | Open. Closed by parity pattern (a) Output Contract single-source file. |
| **MEDIUM** | 86ccb59 — skip deterministic routes on chrome turns | Open. Consider lane-gated re-enable for nav phrases. |
| **MEDIUM** | 09293bb — loop_fsm.py | Pending integration check (`_m.handle()` retry path). |
| **RESOLVED** | 0a71d43 — JOB APPLICATION INTENT, CLOUD_SYSTEM only | Closed by `a079bd4` (Codex, 2026-05-17 20:18:52). |
| **NONE** | db54f18 — LOOP TERMINATION mirror | Corrective, not a regression. |

## Recommended order for the rest of the parity rebuild

1. Pattern (a) Output Contract single-source — closes the HIGH severity from a011cae.
2. Pattern (f) Action chaining rule — orthogonal, low coupling.
3. Pattern (b) FSM split AWAITING_INFO vs AWAITING_CONFIRM — clarifies state semantics before tightening 555cc09's ack classifier.
4. Tighten 555cc09 ack classifier (directive-token gate or lane-gate) — closes CRITICAL.
5. Pattern (c) JSON output contract (6-field) — bigger change, lands after the smaller wins.
6. Pattern (d) Element indexing — server-side serializer change.
7. Pattern (e) Screenshot ground-truth + loop detection — promotes Quick Mode, last because it's the largest behavioral shift.

Author: Claude Code, 2026-05-17 evening session, against `clone/parity-rebuild`.
