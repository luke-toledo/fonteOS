# Daily Cycle Automation

Setting up an automated daily agent that scans your task manager, classifies work, executes what it can, and reports what needs you.

---

## What the daily cycle does

The cycle is a non-interactive Claude session that runs on a schedule. It:

1. Scans all task manager projects for tasks in your "Now" section
2. Classifies each task — what type of work is it, can an agent help, what context is needed
3. Executes what it can — research, drafts, analysis — and writes results to the vault
4. Posts summaries as comments on the relevant tasks
5. Tags tasks that need human input and leaves clear notes on why
6. Writes a daily log at `logs/daily/YYYY-MM-DD.md`

The output is: work done autonomously, and a clear handoff of what still needs you.

---

## Prerequisites

- A task manager MCP configured (Asana or Linear) — see [mcp-integrations.md](../mcp-integrations.md)
- `source/stream-mapping.yaml` with your project/spring mappings
- The `/cycle` command file in `.claude/commands/cycle.md`

---

## Setting up the cycle command

Create `.claude/commands/cycle.md`:

```markdown
Execute the daily agent cycle.

## Gate check

Check if `logs/daily/[today's date].md` exists. If it does, say "Cycle already
ran today. Run again?" and wait for confirmation before proceeding.
(Skip this check on manual /cycle runs if the user explicitly says to re-run.)

## Step 1 — Scan

Read `source/stream-mapping.yaml` to get the list of projects and their
context files.

Fetch all open tasks from the "Now" section of each mapped project via the
task manager MCP.

## Step 2 — Filter

Skip tasks that are:
- Completed
- Tagged `agent-skip` or `human-only`
- Tagged `agent-pending` (already have a proposal awaiting review)

## Step 3 — Classify

For each remaining task, determine:
- Which spring it belongs to
- What type of work it is (research, content draft, analysis, coordination, decision)
- Whether an agent can help autonomously, or whether it needs human input

Tag with `human-only` if: the task requires a meeting, a call, a physical action,
a financial decision, or judgment that the agent can't make without more context.
Post a comment explaining why.

## Step 4 — Execute

For tasks where the agent can help:

**Research/analysis tasks:**
1. Load context files from stream-mapping.yaml
2. Do the research (use Perplexity MCP if needed)
3. Write output to `springs/[spring]/content/YYYY-MM-DD-[topic].md`
4. Post a summary comment on the task with key findings and the file path
5. Tag the task `agent-completed`

**Content draft tasks:**
1. Load context files + `intelligence/voice.md` if it exists
2. Draft content, write to `springs/[spring]/content/YYYY-MM-DD-[topic].md`
3. Post a summary comment on the task with the draft status and file path
4. Tag the task `agent-completed`

**Unclear or complex tasks:**
1. Post a comment: what the agent understood, what it would need to proceed
2. Tag `agent-blocked`

## Step 5 — Report

Write `logs/daily/YYYY-MM-DD.md` with:
- Tasks processed (count by type)
- Tasks completed by agent (list with file paths)
- Tasks tagged human-only (list with reasons)
- Tasks blocked (list with what's needed)
- Any errors or MCP failures

Delete log files older than 7 days from `logs/daily/`.

Post a brief status update as a comment on a designated status task in the
task manager (if one is configured in stream-mapping.yaml).
```

---

## The "Now" section as execution surface

The cycle only processes tasks in your designated "Now" section. This is intentional — you control what the agent touches by what you move into Now.

The handshake:
- **You** drag tasks into the Now section when they're ready for agent processing
- **The cycle** processes Now tasks and moves on
- **You** review what the agent did, approve or redirect

Don't put tasks in Now that you don't want the agent touching. Use a different section (Backlog, Next Week, etc.) for tasks that are prioritized but not yet agent-ready.

If you want the agent to skip a specific task even if it's in Now, tag it `agent-skip`. The cycle will pass over it.

---

## Scheduled triggers

The cycle runs manually via `/cycle` or automatically on a schedule. To set up scheduled runs:

**Using the FonteOS schedule skill** (if available):

```
/schedule daily at 6am: /cycle
```

**Using cron + Claude Code CLI:**

```bash
# Add to crontab: crontab -e
0 6 * * * cd /path/to/your-vault && claude --no-interactive --command "/cycle" >> logs/cycle-cron.log 2>&1
```

Replace `/path/to/your-vault` with your actual vault path.

**Timing considerations:**
- Run it before your workday starts — so the report is ready when you sit down
- Don't run it more than once a day unless you're experimenting (the gate check prevents duplicate runs anyway)
- If you're in a timezone with significant task updates overnight, set the cycle time accordingly

---

## Stream-mapping.yaml reference

The cycle reads this file to know which projects to scan and what context to load per task:

```yaml
# source/stream-mapping.yaml

# Status task for daily cycle updates (optional)
status_task_gid: "0000000000"

projects:
  - spring: product
    task_manager: asana
    project_gid: "1234567890"
    now_section_gid: "1111111111"
    context_files:
      - springs/product/product_index.md
      - springs/product/stream/roadmap.md
      - intelligence/market-landscape.md

  - spring: marketing
    task_manager: asana
    project_gid: "2345678901"
    now_section_gid: "2222222222"
    context_files:
      - springs/marketing/marketing_index.md
      - intelligence/voice.md
      - intelligence/audience.md

  - spring: operations
    task_manager: linear
    project_id: "ops-project-id"
    active_cycle: true
    context_files:
      - springs/operations/operations_index.md
```

The `context_files` list tells the cycle what to load before working on tasks from that project. Keep it lean — load what's relevant to the project, not everything.

---

## Task tags and their meaning

The cycle uses tags to communicate state. These are readable by both the agent and you.

| Tag | Set by | Meaning |
|-----|--------|---------|
| `agent-pending` | Agent | Proposal awaiting your review |
| `agent-approved` | You | You approved, agent should execute next cycle |
| `agent-completed` | Agent | Agent finished, you can verify |
| `agent-blocked` | Agent | Agent can't proceed, needs your input |
| `agent-skip` | You | Agent should not touch this task |
| `human-only` | Agent | Agent assessed: nothing useful it can do here |

The cycle doesn't touch tasks tagged `agent-skip` or `human-only`. If you want a `human-only` task reconsidered, remove the tag and move it back to Now.

---

## Daily log format

```markdown
---
date: YYYY-MM-DD
type: daily-cycle
---

# Daily Cycle — YYYY-MM-DD

## Summary

- Tasks processed: N
- Completed by agent: N
- Human-only: N
- Blocked: N
- Errors: N

## Completed

- **[Task name]** ([spring]) — [brief description of what was done]
  File: `springs/[spring]/content/YYYY-MM-DD-[topic].md`

## Human-only

- **[Task name]** ([spring]) — [reason: requires judgment / meeting / financial decision / etc.]

## Blocked

- **[Task name]** ([spring]) — [what the agent needs to proceed]

## Errors

- [MCP failure or other issue, if any]
```

Logs are stored at `logs/daily/YYYY-MM-DD.md` and deleted automatically after 7 days.

---

## What the agent can and can't do

**Can do autonomously:**
- Research tasks (Perplexity search, synthesis, structured output)
- Draft content (using voice profile and context files)
- Competitive analysis, market research
- Summarizing source material
- Organizing and structuring information

**Requires human involvement:**
- Decisions (the agent surfaces options, you choose)
- Relationship tasks (calls, meetings, follow-ups)
- Financial actions
- Publishing or sending anything externally
- Anything requiring judgment beyond the available context

The cycle is not trying to run your entire operation. It's trying to eliminate the research/drafting/synthesis work that takes time but doesn't require your specific judgment — so when you sit down, you're reviewing and deciding, not starting from scratch.
