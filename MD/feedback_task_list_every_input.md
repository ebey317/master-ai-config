---
name: feedback_task_list_every_input
description: Every user input is evaluated for task signals before responding. If the input names work, add a TaskCreate. If it cancels/completes/redirects work, TaskUpdate. Keep the list current; clear stale entries proactively. Born from 2026-05-17 when an email Elijah expected was silently dropped.
metadata:
  type: feedback
---

Every user message is scanned for task signals before you reply. If the user names new work, call `TaskCreate`. If they cancel, complete, or redirect existing work, call `TaskUpdate`. Clear stale entries proactively — a task list cluttered with abandoned items loses signal. The list is a contract, not a notepad: items on it WILL be tracked through completion or explicit removal.

**Why:** 2026-05-17 — Elijah surfaced that I had committed to sending him an email in a prior session and never did. With multiple threads in flight (Master AI build + job search + extension architecture + memory upkeep), silent drops happen when there's no explicit list holding promises. Elijah works ADHD-style: he names many things in one message and trusts that the ones tagged as work will be executed or surfaced. Without a list, my "I'll handle that" lines evaporate when the conversation moves. The cost is real: he expected an email, didn't get one, and discovered the gap days later.

**How to apply:**

1. **At the start of every reply**, before composing prose, mentally scan the user's message for task signals: "send me X", "fix Y", "file Z", "remember W", deadlines, commitments, things-I-said-I-would-do. Call `TaskCreate` for each. ONE tool call per assistant message — that's the standing rule from `feedback_and_means_sequential`. Multiple tasks fire as multiple TaskCreate calls across multiple messages, sequentially.
2. **When starting work on a task**, call `TaskUpdate` to mark `in_progress` BEFORE the work begins. The spinner gives Elijah live signal that the item is being touched.
3. **When finishing a task**, call `TaskUpdate` to mark `completed` immediately. Don't batch completions. Then within the same workflow (or by end of the reply that completes it), call `TaskUpdate` again with `status: deleted` to clear it from the list — Elijah's standing instruction 2026-05-17: completed tasks should not accumulate on the active board. The completion is recorded in the description and the underlying commits/files; the list is for what's actively pending or in-progress only.
4. **When the user cancels, supersedes, or redirects**, call `TaskUpdate` with `status: deleted` (or `completed` if absorbed into another task). Keep the list honest.
5. **At end of every session-shaped milestone** (commit landed, restart performed, doc copied, decision made), sweep `TaskList` and verify each `pending` entry is still actionable. Stale items get explicit `deleted` calls with a note in the description.
6. **Never make a commitment outside the task list.** If you tell Elijah "I'll send that email" without a TaskCreate, the promise has no anchor and will drop. Either capture it, or don't make the commitment.

Failure mode is silent: dropped promises don't error, they just vanish. The list is the safety net.

Related: [[feedback_no_staged_as_status]] (only DONE or NOT STARTED — same epistemic discipline applied to a different surface). [[feedback_real_fixes_not_option_menus]] (one root-cause fix at a time — keeps the in_progress list small). [[feedback_and_means_sequential]] (one tool_use per assistant message — TaskCreate calls obey the same rule).
