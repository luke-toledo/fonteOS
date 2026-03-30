# Core Concepts

The mental model behind FonteOS. Read this once — it makes everything else obvious.

---

## Source

Source is `core.md`. One file. Read every session. It contains your identity and mission, your strategic priorities, a registry of all your springs and intelligence files, recent shifts across your work, and open decisions. It's the file that makes Claude useful across conversations — without it, you re-explain your context every session.

Claude reads Source at every `/start` but never writes to it without your approval. You update it weekly (or when something meaningful shifts). If `core.md` is stale, the whole system drifts. Keep it current.

---

## Springs

A spring is a project or venture — the container for everything related to one initiative. Each spring has an index file (`[name]_index.md`) that lists its active streams, referenced intelligence files, key people, recent decisions, and open questions. The index is the entry point Claude reads when you scope a session to that project.

Springs are self-contained. Each session declares which spring it's working in and restricts writes to that path. This is what makes parallel sessions possible — two Claude Code windows can work simultaneously on different springs without stepping on each other's files.

---

## Streams

Streams are where daily work happens — the active workstreams within a spring. A stream file is a markdown document covering one topic area: a product decision, a marketing approach, a research thread, a client relationship. Claude writes to streams freely. They're meant to be messy and fast, not permanent.

When a stream is done, it either gets archived or its insights get graduated to Intelligence. Streams that go untouched for 30+ days show up in `/audit` as stale. Don't let streams accumulate — they're work in progress, not storage.

---

## Intelligence

Intelligence is the library — reference knowledge that's been vetted and promoted from stream work. Intelligence files are slow-changing, confidence-rated (`high / medium / low / speculative`), and loaded on demand when a stream references them. They're not auto-loaded at startup; Claude pulls them when needed.

Every intelligence file earns its place through the graduate protocol: when a stream insight recurs three or more times across sessions, it gets promoted to a permanent intelligence file. This keeps streams lightweight and intelligence files high-signal. Intelligence files require human approval to create or modify — Claude proposes, you crystallize.

---

## Agents

An agent file defines an AI persona for a specific job. It specifies the role, the rules, the intelligence files the agent should load, and any constraints. Examples: a content agent that loads your voice profile before drafting, a researcher that structures Perplexity output into intelligence files, an operations agent that handles routine file maintenance.

Agents are not auto-loaded. You load an agent file explicitly before delegating that type of work. They're useful when you want consistent behavior across sessions for a specific task type — the agent file encodes the setup so you don't have to re-explain it each time.

---

## Inbox

Inbox is the catch-all for ideas that don't fit the current session scope. Claude drops things there automatically rather than derailing the current workstream. You triage inbox weekly — each item either gets turned into a proper file (stream, intelligence, note), deleted, or kept for later.

Inbox items older than two weeks show up in `/audit` as stale. Don't use inbox as storage. It's a buffer, not a backlog.

---

## The two control files

**`CLAUDE.md`** is Claude's instruction file. It lives at the vault root and is loaded automatically at the start of every session. It defines how Claude behaves: session startup protocol, write permissions, standing rules, vault navigation. You edit it when you want to change Claude's behavior — but treat it as infrastructure. Casual edits break things. Claude is never permitted to modify it.

**`source/core.md`** is your file. It defines what you're building and what matters. Claude reads it but treats edits as requiring human approval. The two files together answer: "How should Claude work?" (CLAUDE.md) and "What is Claude working on?" (core.md).

---

## Wikilinks and the graph

Every file in the vault uses `[[wikilinks]]` to connect to related files. Obsidian visualizes these as a graph — you can see which projects connect to which intelligence, which streams reference the same research. An isolated file with no connections is a failure state. Claude is instructed to add at least one outgoing wikilink to every file it creates.

The graph isn't decoration. It's the audit surface. During `/audit`, Claude scans for orphaned files — intelligence files referenced by nothing, streams disconnected from their spring index. Orphans get flagged for archiving or connection.

---

## Write permissions

Not all layers are equally editable. This is by design.

| Layer | Claude can write? |
|-------|-------------------|
| Streams | Yes — freely |
| Inbox | Yes — freely |
| Session state | Yes — auto-written at /end |
| Spring indexes | Propose only — human approves |
| Intelligence | Propose only — human approves |
| Source (core.md) | Propose only — human approves |
| CLAUDE.md | Never |

The boundary is between operational files (streams, inbox, session state) and permanent records (indexes, intelligence, source). Claude works fast in the operational layer and is deliberate about the permanent layer.

---

## Graduate protocol

Insights start in streams — messy, provisional, session-specific. When the same insight shows up in three or more sessions, it's earned a permanent home. At that point, you graduate it: promote it from stream notes into a proper intelligence file with a confidence rating, frontmatter, and wikilinks.

This is how knowledge accumulates without bloat. Streams stay lightweight. Intelligence files stay high-signal. Nothing important gets lost; nothing trivial gets promoted prematurely.

---

## Token efficiency

The vault structure exists to keep Claude's loaded context small. At `/start`, Claude loads `core.md` and `session-state.md` — roughly 1-2k tokens. When you pick a project, it loads the spring index and relevant stream files — another 2-3k. Intelligence files load only when referenced. Total active context: around 5k tokens, with 90%+ of the context window free for actual work.

The alternative — stuffing all your context into a system prompt, or loading every file every session — burns tokens and degrades output quality. MCP integrations follow the same principle: external data (tasks, research, design files, documents) stays external and gets fetched on demand rather than pre-loaded into the prompt. The system is architecturally lazy about context loading, and that's the point.
