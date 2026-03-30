# Multi-Session Work

Running multiple Claude Code sessions in parallel. How spring isolation makes this safe, and where the edges are.

---

## Why this works

FonteOS is designed for parallel sessions. The spring isolation constraint — each session declares one spring and restricts writes to that path — means two sessions working on different springs can run simultaneously without conflict.

In practice: open two terminal windows, run `claude` in each, `/start` in each. Session A scopes to `springs/product/`, Session B scopes to `springs/freelance/`. They write to separate directories. No collision.

---

## The rules

**Spring isolation is hard.** Each session must declare a spring and restrict writes to that spring's path. Cross-spring writes require explicit approval from the human — not just Claude deciding it's fine.

**Core.md is single-writer.** Only one session should propose edits to `source/core.md` at a time. If both sessions want to update core.md in the same work block, do them sequentially. Concurrent edits to the same file = merge conflict.

**Intelligence is single-writer per work block.** Same rule as core.md. Intelligence files are slow-changing by design — they shouldn't need parallel writes. If two sessions both want to update `intelligence/topic.md`, coordinate.

**Spring indexes are single-writer.** Each spring's `[name]_index.md` should only be modified by the session scoped to that spring. Session B shouldn't be modifying `springs/product/product_index.md`.

**Streams are safe to parallelize.** Multiple sessions writing to different stream files in different springs is fine. That's the point.

---

## Session state doesn't parallelize

`source/session-state.md` is a single file overwritten at every `/end`. If two sessions both run `/end` around the same time, the second one overwrites the first. You'll lose one session's state.

**How to handle this:**
- End sessions sequentially, not simultaneously
- Or accept that only the last `/end` matters for next-session pickup
- If you lose state from a parallel session, the work still exists in the stream files — you just don't get the pickup summary

Session state is operational metadata. If it gets overwritten, the work isn't lost. The summary is lost.

---

## Conflict scenarios and how to avoid them

**Scenario 1: Both sessions want to update core.md**

Session A decides priorities changed. Session B decides to add a new spring to the registry. Both propose core.md edits at `/end`.

Resolution: Apply them sequentially. Run the first `/end`, apply the core.md edit, then run the second `/end` and apply that edit. Don't approve both simultaneously — you'll be merging diffs by hand.

**Scenario 2: One session reads a file the other is actively writing**

This is generally fine for reads. Markdown files aren't databases — there's no lock contention. If Session B reads `springs/product/stream/pricing.md` while Session A is writing it, Session B gets whatever's on disk at read time. Worst case: stale context for Session B.

If real-time coordination matters, scope the sessions so they don't read each other's active write targets.

**Scenario 3: Both sessions try to update the same intelligence file**

Don't let this happen. Intelligence files should only be updated by the session working in the relevant spring. If two springs share an intelligence file, designate one session as the writer and have the other read-only for that file.

**Scenario 4: Task manager conflict**

If both sessions are connected to a task manager (Asana, Linear), be careful about concurrent task updates. Two sessions proposing updates to the same task is fine — but if you approve both simultaneously, you may get double comments or conflicting status updates. Run task manager syncs sequentially at `/end`.

---

## Practical setup for parallel work

If you regularly run parallel sessions, a few conventions help:

**Name your terminal windows/tabs by spring.** Reduces the cognitive overhead of tracking which session is which.

**Stagger your /end calls.** Don't run `/end` in both sessions at the same minute. Give each session a clean close.

**Keep core.md writes rare.** Core.md should change weekly at most. If you're updating it every session, the parallel session constraint becomes painful. Batch core.md updates into one designated session per week.

**Use git.** The vault is a git repo. Running `git status` after a session shows what changed. If a merge conflict appears, it's almost always a sign that two sessions violated the single-writer rule on a shared file. Fix it there.

---

## Git as the coordination layer

With git, you have a natural conflict detection system. After each session's `/end`:

```bash
git add -A
git commit -m "session: [spring] [date]"
git push
```

If two sessions committed conflicting changes, `git push` on the second one will fail. That's the signal to merge manually. For stream files, this is rare because streams don't usually share content. For shared files (core.md, intelligence), it's a sign you violated the single-writer rule.

Obsidian Git plugin can automate the commit/push cycle. See its docs for auto-commit on vault close.

---

## The upper limit

There's no hard limit on parallel sessions, but cognitive overhead grows fast. Two parallel sessions is natural — one for the primary project, one for something that needs attention. Three is manageable. Four or more and you're spending more time coordinating than working.

The system optimizes for one deep session at a time with the occasional parallel session for interrupts. It's not designed for a swarm of agents all writing simultaneously. For automated agent pipelines at scale, see [advanced/daily-cycle.md](daily-cycle.md).
