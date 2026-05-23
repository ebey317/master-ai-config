---
name: Optional "Face / Bubble" UI Mode for Claude
description: Elijah's 2026-04-20 idea — when Claude is just talking (not actively coding), he'd like to see a face or a simple bubble/avatar. When actively coding, keep the current diff-stream view. Product idea, not built yet.
type: user
originSessionId: c53f933b-955f-4b1d-8b2b-dc75f15b261b
---
**Elijah's own words (2026-04-20):**

> "I'd like to see your face, and you know, of course if I get to see the stuff come down, your face and make that look cool, that'll be all right. No actually I would rather see it the way I see it [when coding]. But if we're just actively talking, I'd rather see your face or just a bubble box."

**What he's describing — two-mode view:**

- **Coding mode (current default):** diffs, tool calls, text streaming down — keep exactly as is. He likes seeing the work happen.
- **Talking mode (new):** when the exchange is conversational (no edits, no tool output), swap to a simple face or chat-bubble view. Something that feels like a person, not a terminal. De-emphasize "AI is typing"; emphasize "we're having a conversation."

**Why this matters:**

- The voice-to-text workflow means he's often just talking to me from his phone. A wall of text isn't the right feel for a conversation.
- Pairs with the product framing "genius right next to you" — a face makes the "next to you" real.
- This is the same realization as the Sensei-inherits-Claude-profile work (2026-04-20): once Sensei has Elijah's memory, it's not a tool anymore. A face is the visual version of that same promise.

**Where this would live:**

- NOT in Claude Code itself (that's Anthropic's product — not ours to modify).
- COULD live in Sensei's tmux UI as a rendering mode. When the last N messages are all conversational, swap to an avatar/face pane. When a tool call happens, swap back to the diff view.
- COULD live in Pupil as a chat-bubble theme — probably easier to prototype first (browser is more forgiving for avatars).

**How to apply:**

- Do NOT build this proactively. It's an idea, not a commitment.
- When Elijah opens the phase (he'll say something like "let's do the face thing" or "build the bubble mode"), start with Pupil — easier to iterate visually in a browser.
- Keep the diff-stream view as the dominant mode. The face is the secondary / conversational state, not a replacement.
- Pairs with `project_pitch_genius_beside_you.md`, `project_materializing_whitespace.md`, and the 2026-04-20 Sensei-inherits-profile architecture.
