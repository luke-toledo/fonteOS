# Customization

How to adapt FonteOS to your actual work. Adding springs, creating streams, building intelligence, writing slash commands, defining agents.

---

## Adding a spring

A spring is a folder in `springs/` with an index file. Create one when you're starting a new project or venture that deserves its own isolated context.

**Folder structure:**
```
springs/
└── my-new-project/
    ├── my-new-project_index.md
    └── stream/
        └── .gitkeep
```

**Index file template:**
```markdown
---
type: spring-index
spring: my-new-project
status: active
tags: [spring-index]
updated: YYYY-MM-DD
---

# My New Project

> Connected to: [[core]]
> **Status:** Active

## Scope

What this project covers. One or two sentences.

## Active streams

| Stream | Scope | Status | Last touched |
|--------|-------|--------|--------------|
| | | | |

## Referenced intelligence

<!-- Links to intelligence files relevant to this project -->

## Key people

<!-- Collaborators, stakeholders, advisors -->

## Recent decisions

<!-- Date: decision made -->

## Open questions

<!-- What's unresolved -->
```

Then add the spring to the registry in `source/core.md`. Claude can propose this edit at `/end`, or you can do it manually.

---

## Creating streams

Streams are markdown files in `springs/[spring]/stream/`. Create one per topic area — a product decision, a research thread, a client relationship, a content strategy.

**Stream file template:**
```markdown
---
type: stream
spring: my-project
status: active
tags: [my-project, topic-name]
updated: YYYY-MM-DD
---

# Topic Name

> Connected to: [[my-project_index]], [[relevant-intelligence-file]]

## Context

Why this workstream exists. What problem it's solving.

## Current state

What's happening now.

## Working notes

[Daily work goes here — Claude writes freely]

## Open questions

- Question 1
- Question 2

## Decisions log

- YYYY-MM-DD: Decision made and why
```

You can also create streams via `/note` — Claude will prompt you for the details and handle the file creation with proper wikilinks.

---

## Building intelligence files

Intelligence files live in `intelligence/`. They're reference knowledge — slower-changing than streams, higher confidence, loaded on demand.

Create an intelligence file when:
- A stream insight has recurred 3+ times (graduate protocol)
- You've done deep research that should be reusable across sessions
- There's stable reference material Claude should be able to load when relevant

**Intelligence file template:**
```markdown
---
type: intelligence
confidence: high        # high / medium / low / speculative
tags: [topic, scope]
updated: YYYY-MM-DD
---

# Topic Name

> **Confidence:** High — [brief note on source quality]
> Connected to: [[relevant-stream]], [[core]]

## Summary

2-3 sentence overview. What this file is for.

## Key findings

- Finding 1
- Finding 2
- Finding 3

## Detail

Deeper content organized by subtopic.

## Sources

- Source 1 — [URL or reference]
- Source 2

## What this changes

How this intelligence affects decisions or priorities.

## What's still unknown

Gaps, caveats, things to research further.
```

Add every new intelligence file to the intelligence registry in `core.md`. Also link it from any spring index or stream that references it.

---

## Writing custom slash commands

Slash commands live in `.claude/commands/` as markdown files. Each filename becomes a `/command` you can trigger in any session. The file content is the prompt Claude follows when you trigger it.

**Basic command structure:**
```markdown
# Command Name

Brief description of what this command does.

## Steps

1. Step one
2. Step two
3. Step three

Use $ARGUMENTS for user input passed after the command name.
```

**Research command** — wraps a research MCP with structured output:
```markdown
Deep research on the topic provided by the user.

The user's topic is: $ARGUMENTS

Steps:
1. Use the Perplexity MCP to research the topic. Prioritize primary sources.
2. Present findings:
   - Summary — 2-3 sentence overview
   - Key findings — 5-10 bullet points
   - Sources — URLs with brief descriptions
   - Confidence — assess source quality (high/medium/low/speculative)
3. Ask: "Save as intelligence file, drop in inbox, or just use it here?"
   - If intelligence: create the file with proper frontmatter and wikilinks
   - If inbox: drop a brief summary in inbox/
   - If here: done
```

**Video extraction command:**
```markdown
Analyze a YouTube video using Gemini.

URL: $ARGUMENTS

Steps:
1. Extract video content using the Gemini MCP.
2. Present:
   - Title and creator
   - Summary — 3-5 sentences
   - Key points with approximate timestamps
   - Notable quotes worth saving
   - Credibility notes — distinguish research, opinion, anecdote, speculation
3. Ask: "Save as intelligence file, drop in inbox, or just use it here?"
```

**Quick capture command** (for ideas that don't need a full note):
```markdown
Capture a quick idea without ceremony.

Idea: $ARGUMENTS

1. Write a brief note to inbox/ with:
   - Date
   - The idea as stated
   - One sentence on why it might matter
   - Tag it: #idea #triage
2. Confirm: "Dropped in inbox."
```

Place all command files in `.claude/commands/`. They're available immediately — no restart needed.

---

## Defining agents

Agent files live in `agents/`. An agent is a defined AI role with specific instructions, intelligence references, and constraints. Load an agent file explicitly before delegating that type of work.

**Agent file template:**
```markdown
---
type: agent
role: [role name]
model: [sonnet / opus / gemini / etc.]
tags: [agent, role-name]
updated: YYYY-MM-DD
---

# Agent Name

> Connected to: [[core]], [[relevant-intelligence]]

## Role

What this agent does. One sentence.

## When to load this agent

Specific triggers — what type of request should use this agent.

## Intelligence to load

- [[intelligence/file-one]] — why it's relevant
- [[intelligence/file-two]] — why it's relevant

## Instructions

Detailed behavioral rules for this agent role.

1. Rule one
2. Rule two
3. Rule three

## Output format

What this agent should produce. Structure, length, format.

## Constraints

- What this agent should never do
- Boundaries
```

**Example: content agent**
```markdown
---
type: agent
role: content
model: claude-sonnet-4-5
tags: [agent, content]
updated: 2026-03-30
---

# Content Agent

> Connected to: [[core]], [[intelligence/voice]]

## Role

Draft content (posts, essays, scripts) in the operator's voice.

## When to load this agent

When drafting anything intended for public-facing channels.

## Intelligence to load

- [[intelligence/voice]] — voice profile, always load first
- [[intelligence/audience]] — who we're writing for

## Instructions

1. Load intelligence/voice.md before writing anything.
2. Match the voice profile exactly — tone, rhythm, sentence length.
3. Propose, don't publish. All content drafts require human approval.
4. Flag if the brief conflicts with voice principles.
5. Write a first draft, then apply /humanize before presenting.

## Output format

First draft in full. Then brief notes on where you made judgment calls.

## Constraints

- Never soften positions to avoid controversy
- No hedging language ("it could be argued that...")
- Never write superlatives without evidence
```

---

## Modifying CLAUDE.md

CLAUDE.md is the behavioral foundation. Modify it deliberately — it affects every session.

The main things you'd customize:

**Session startup** — add steps, change the order, modify what gets loaded first.

**Standing rules** — add project-specific conventions, change the graduate threshold (default: 3 recurrences), adjust orphan audit frequency.

**Write permissions** — add services or integrations to the autonomous/propose/never tiers.

**Vault navigation** — update the path table if you rename folders.

Make one change at a time. Test it in a session. Don't refactor CLAUDE.md wholesale — small, deliberate changes only.
