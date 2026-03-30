# Session Lifecycle

How a session starts, runs, and closes. What Claude reads, when, and why.

---

## /start

When you type `/start`, Claude executes a fixed sequence:

**1. Read `source/core.md`**
Claude orients on your mission, current priorities, recent shifts, and spring registry. This is the session foundation — everything else builds on it.

**2. Read `source/session-state.md`**
If a previous session exists, Claude surfaces it briefly:
> "Last session: 2026-03-28, scoped to [spring]. You were working on [streams]. Next priority was: [sentence]."

If this is the first session ever, session-state.md is empty and Claude skips this step.

**3. Task manager sync (if configured)**
If you've connected Asana or Linear via MCP, Claude pulls "Now" tasks for each project and presents a combined briefing alongside the vault stream status. This gives you two views at once: strategic direction from the vault, execution items from your task manager.

**4. Declare scope**
Claude announces which spring this session is working in and restricts writes to that path. If you have obvious next work from session-state or task manager, it suggests it. Otherwise it offers 2-3 options based on priority and recency.

**5. You pick**
Claude loads the relevant spring index (`[spring]_index.md`) plus the stream files you're working in. If those streams reference intelligence files, Claude reads those too.

**6. Work begins**
Only after all required files are loaded. Never before.

---

## During a session

Once started, Claude operates with spring isolation. A few things that matter:

**Writes stay in scope.** Claude writes freely to stream files within the declared spring. Everything else — spring indexes, intelligence files, core.md — requires your approval before changes are made.

**Stray ideas go to inbox.** If something comes up that doesn't belong to the current stream or spring, Claude drops it in `inbox/` with a note rather than derailing the session.

**Cross-spring reads are always permitted.** Claude can read files from other springs for context. Cross-spring writes require explicit approval.

**Claude makes decisions when direction is clear.** It only pauses for genuinely ambiguous choices. If you ask it to research something, it researches. If you ask it to draft, it drafts. You're not a prompt engineer — you're a collaborator.

**MCP calls are on demand.** If you ask Claude to look something up via Perplexity, pull a task from Asana, or read a Figma file, it calls the MCP when you need it — not at startup.

---

## What "upstream" means

At the end of a session, you have two choices for each piece of work:

**Upstream** — codify the output into a permanent file. This means Claude proposes specific edits to stream files (it writes directly), or proposes edits to spring indexes, intelligence files, or core.md (you approve those). The insight or decision becomes a durable artifact in the vault.

**Dispose** — close the conversation without extracting anything. Sometimes a session is just working through something and the conversation itself is the output. Dispose is not failure — it's a legitimate choice. Not every session needs to produce a file.

The upstream/dispose decision happens at `/end` for each piece of work. You don't have to upstream everything. Be selective — only promote things that will matter later.

---

## /end

When you type `/end`, Claude runs the close protocol in four steps:

**1. Session summary**
Claude produces a concise summary:
- **Decided:** Key decisions made this session
- **Changed:** Files updated, tasks completed
- **Next:** Open tasks, blockers, suggested priority for next session

**2. Task manager sync (if configured)**
Claude proposes updates to your task manager: complete tasks that finished, comment on tasks that progressed but aren't done. Each proposed action is presented for your approval before execution. Nothing happens without confirmation.

**3. Upstream or dispose**
Claude asks for each piece of work. Stream files it can write directly; permanent files it proposes. You review proposals and approve or reject.

**4. Write session-state.md**
Claude overwrites `source/session-state.md` with a structured summary of the session. This is the bridge to the next conversation. It happens automatically — no approval needed. Session state is operational metadata, not knowledge.

---

## Session state as the bridge

`session-state.md` is always overwritten — only the most recent session matters. It contains:

```markdown
## Last session

- **Date:** YYYY-MM-DD
- **Spring scope:** [spring]
- **Streams:** [what was worked on]
- **Decided:** [key decisions]
- **Changed:** [files updated, tasks completed]
- **Open tasks:** [what's left]
- **Blockers:** [what's stuck]

## Next session priority

[One-sentence description of where to pick up]
```

When you start your next session, Claude reads this and picks up cold. You don't re-explain what you were working on. You don't re-establish context. The loop is: `/start`, Claude briefs you on where you left off, you confirm or redirect, work resumes.

---

## Spring isolation

Each session is scoped to exactly one spring. This is a hard constraint, not a guideline.

**Why it matters:** If you run two Claude Code sessions simultaneously — say, one for a product spring and one for a freelance spring — isolation prevents them from stepping on each other's files. Session A writes to `springs/product/stream/`, session B writes to `springs/freelance/stream/`. They don't collide.

**What isolation means in practice:**
- Writes are restricted to the declared spring's path
- Cross-spring edits require you to explicitly approve a "cross-over"
- `core.md`, `intelligence/`, and spring indexes are single-writer — only one session touches them per work block
- Cross-spring reads are always permitted

For more detail on running parallel sessions, see [advanced/multi-session.md](advanced/multi-session.md).

---

## The full loop

```
/start
  → reads core.md + session-state.md
  → (optional) pulls task manager "Now" tasks
  → declares scope, suggests work
  → you pick
  → loads spring index + stream files
  → work begins

[work]
  → Claude writes streams freely
  → proposes changes to permanent files
  → MCP calls as needed
  → inbox for strays

/end
  → summarizes: decided, changed, next
  → (optional) proposes task manager updates
  → upstream or dispose
  → writes session-state.md
```

The loop is fast when you run it consistently. Skipping `/end` breaks session continuity — the next `/start` will surface stale state. Skipping `/start` means Claude doesn't know your context. Run both.
