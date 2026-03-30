# Getting Started

From zero to your first working session. No server, no build step, no account.

---

## Prerequisites

- **Claude Code** — the Anthropic CLI. Required. Install at [claude.ai/code](https://claude.ai/code).
- **Git** — for cloning and version control.
- **Obsidian** — optional but recommended. If you skip it, any markdown editor works — you just won't have the graph view.

---

## Step 1 — Clone

```bash
git clone https://github.com/ShrimpPasta/fonteos.git my-vault
cd my-vault
```

Replace `my-vault` with whatever you want to call your vault directory.

---

## Step 2 — Open in Obsidian (optional)

In Obsidian, go to **Open folder as vault** and select your cloned directory. This gives you graph visualization of how your files connect — useful but not required to run the system.

---

## Step 3 — Start Claude Code

Open a terminal in your vault directory:

```bash
claude
```

Claude Code loads `CLAUDE.md` automatically. The behavioral rules are already active before you type anything.

---

## Step 4 — First /start

Type:

```
/start
```

Claude detects that `source/core.md` is empty and runs the first-run setup:

1. **Who are you and what are you building?** — Claude populates your identity and mission.
2. **Top 2-3 priorities** — what matters most right now, and on what timeline.
3. **Task manager** — connect Asana, Linear, or choose vault-only mode (you can add this later).
4. **First spring** — Claude creates your first project from priority #1: the spring index and stream directory.

The whole setup takes 5-10 minutes of back-and-forth. Answer directly — stream of consciousness is fine. Claude structures it.

---

## Step 5 — Your first work session

After setup, Claude declares scope and asks what you want to work on. It suggests 2-3 options based on what you just told it.

Pick one. Claude loads the relevant spring index and stream files, then asks: **"What's first?"**

From here, just work. A few things to know:

- **Claude writes to streams freely.** Ask it to draft, research, analyze, outline — it goes directly into your stream files.
- **Changes to permanent files require your approval.** If Claude wants to update your spring index or intelligence files, it proposes the edit and waits.
- **Stray ideas go to inbox.** If something doesn't fit the current work, Claude drops it in `inbox/` rather than derailing the session.
- **Cross-spring edits need explicit approval.** Claude stays in its declared spring unless you say otherwise.

---

## Step 6 — Your first /end

When you're done (or out of time), type:

```
/end
```

Claude runs the close protocol:

1. **Summarizes the session** — what was decided, what changed, what's next.
2. **Task manager sync** — if you connected a task manager, proposes updates (complete finished tasks, comment on progressed ones). You approve each one.
3. **Upstream or dispose** — Claude asks if you want to codify outputs into permanent files, or just close. Either is fine.
4. **Writes session-state.md** — automatically. This is the bridge to your next session. Next time you `/start`, Claude reads it and picks up cold.

---

## What the vault looks like after setup

```
my-vault/
├── CLAUDE.md                          ← Behavioral rules (don't edit without knowing what you're doing)
├── source/
│   ├── core.md                        ← Your mission, priorities, spring registry (populated)
│   └── session-state.md               ← Last session summary (written at /end)
├── springs/
│   └── your-first-project/
│       ├── your-first-project_index.md
│       └── stream/
│           └── (streams appear as you work)
├── intelligence/
├── agents/
├── inbox/
└── .claude/
    └── commands/
        ├── start.md
        ├── end.md
        ├── audit.md
        └── note.md
```

---

## From here

Every session follows the same loop: `/start` → work → `/end`. The system grows organically as you use it — streams accumulate, intelligence files graduate from recurring insights, springs multiply as your projects do.

Run `/audit` monthly to clean up orphans and stale files. Run `/note` when you want to create a properly structured new file with wikilinks.

That's it. The loop is the system.
