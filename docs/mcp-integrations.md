# MCP Integrations

FonteOS works without any of these. MCP integrations add capability — task management, research, design access, document reading — but the base system runs entirely on markdown files and Claude Code.

Add integrations as you need them, not all at once.

---

## How MCP works

MCP (Model Context Protocol) lets Claude call external tools during a session. You configure servers in `.claude/settings.json`. Once configured, Claude can call them naturally — no special syntax required, just "look up this task" or "search Perplexity for X."

Data fetched via MCP stays external until needed. Claude doesn't pre-load it — it calls the tool when you or the session protocol asks for it. This keeps the token budget clean.

---

## Asana (recommended task manager)

**What it does:** Pull tasks, create tasks, update status, post comments — all from the terminal. At `/start`, Claude fetches your "Now" tasks per project and presents them alongside vault stream status. At `/end`, Claude proposes task updates (completions, comments) for your approval.

**When to use it:** If you use Asana for task management, this is the primary integration. The "Now" section of each Asana project becomes the shared execution surface — you drag tasks there, Claude processes them.

**Setup:**

Asana's MCP is available through Claude's platform integrations. Enable it in Claude Code settings, then authenticate with your Asana account.

Once connected, map your Asana projects to FonteOS springs in `source/stream-mapping.yaml` (create this file):

```yaml
# source/stream-mapping.yaml
projects:
  - spring: my-project
    asana_project_gid: "1234567890"
    now_section_gid: "0987654321"
    context_files:
      - springs/my-project/my-project_index.md
      - springs/my-project/stream/main-workstream.md

  - spring: second-project
    asana_project_gid: "1111111111"
    now_section_gid: "2222222222"
    context_files:
      - springs/second-project/second-project_index.md
```

Find project and section GIDs by navigating to them in Asana and checking the URL, or by asking Claude to list your projects via the MCP.

**Usage pattern:** Tell Claude at `/start` to pull your tasks. It shows you a combined view of vault streams + Asana "Now" tasks. Work happens in the vault; task status updates happen in Asana via proposals at `/end`.

---

## Linear (alternative task manager)

**What it does:** Same integration pattern as Asana — pull issues, update status, comment — but for teams using Linear.

**When to use it:** If your team is on Linear and Asana isn't in the picture.

**Setup:**

Configure the Linear MCP in Claude Code settings:

```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "@linear/linear-mcp-server"],
      "env": {
        "LINEAR_API_KEY": "lin_api_..."
      }
    }
  }
}
```

Get your Linear API key at linear.app → Settings → API → Personal API keys.

**Usage pattern:** Same as Asana. The "Now" equivalent in Linear is a filtered view or specific cycle — configure that boundary in your stream-mapping file. Ask Claude at `/start` to pull your active Linear issues.

---

## Perplexity (research)

**What it does:** Deep research with sourced, multi-page answers. Good for competitor analysis, market research, community/Reddit mining, technical deep-dives.

**When to use it:** When you need sourced answers — not casual lookups. Claude can answer surface questions from its own knowledge. Use Perplexity when you need current information, specific sources, or community sentiment.

**Setup:**

```json
{
  "mcpServers": {
    "perplexity": {
      "command": "npx",
      "args": ["-y", "@anthropic/perplexity-mcp-server"],
      "env": {
        "PERPLEXITY_API_KEY": "pplx-..."
      }
    }
  }
}
```

Get your API key at perplexity.ai → Settings → API.

**Usage pattern:** Build a `/research` command (see [customization.md](customization.md)) that wraps Perplexity output in a structured format and asks whether to save as an intelligence file. This enforces the habit of deciding what to do with research rather than letting it float in the conversation.

---

## Gemini (video and documents)

**What it does:** YouTube video extraction (timestamped summaries, key points, quotes), document analysis (PDFs, images), and general vision tasks. Useful when you need to process long-form content without watching the whole thing.

**When to use it:** Processing YouTube videos for research, extracting structured data from PDFs, analyzing screenshots or images.

**Setup:**

```json
{
  "mcpServers": {
    "gemini": {
      "command": "npx",
      "args": ["-y", "@anthropic/gemini-mcp-server"],
      "env": {
        "GOOGLE_API_KEY": "AIza..."
      }
    }
  }
}
```

Get your API key at aistudio.google.com.

**Usage pattern:** Build a `/video` command that takes a YouTube URL, extracts content via Gemini, and formats it as a structured summary with a save decision at the end. The output either becomes an intelligence file or gets dropped in inbox.

---

## Google Drive (file access)

**What it does:** Read-only access to Google Sheets and Docs. Pull spreadsheet data, document content, or shared files directly into Claude's context without manual copy-paste.

**When to use it:** When your source of truth for something (costs, timelines, team data, shared research) lives in Google Drive and you need Claude to reference it. Also useful for pulling external data into intelligence files.

**Setup:**

```json
{
  "mcpServers": {
    "google-drive": {
      "command": "npx",
      "args": ["-y", "@anthropic/gdrive-mcp-server"],
      "env": {
        "GOOGLE_CLIENT_ID": "...",
        "GOOGLE_CLIENT_SECRET": "..."
      }
    }
  }
}
```

Follow the OAuth setup instructions in the `@anthropic/gdrive-mcp-server` readme to get your client credentials.

**Usage pattern:** Reference a Google Sheet or Doc by URL during a session. Claude reads it and uses the data in context. If the data is stable and reusable, ask Claude to write a summary into an intelligence file so future sessions don't need to re-fetch.

---

## Figma (design)

**What it does:** Read design files, get component screenshots, extract design tokens, access FigJam boards. Bridges the gap between design files and vault context.

**When to use it:** When you work with designers or maintain a design system and need Claude to understand what's been designed — for code generation, design review, or extracting specs.

**Setup:**

Figma's MCP is available through Claude's platform integrations. Enable it in Claude Code settings and authenticate with your Figma account.

**Usage pattern:** Share a Figma URL during a session. Claude reads the design context, gets a screenshot, and can extract component specs, design tokens, or layout information. If you're generating code from designs, this replaces the copy-paste-spec workflow.

---

## Adding multiple integrations

You can run multiple MCP servers simultaneously. Add each one to the `mcpServers` object in `.claude/settings.json`:

```json
{
  "mcpServers": {
    "perplexity": {
      "command": "npx",
      "args": ["-y", "@anthropic/perplexity-mcp-server"],
      "env": { "PERPLEXITY_API_KEY": "pplx-..." }
    },
    "google-drive": {
      "command": "npx",
      "args": ["-y", "@anthropic/gdrive-mcp-server"],
      "env": {
        "GOOGLE_CLIENT_ID": "...",
        "GOOGLE_CLIENT_SECRET": "..."
      }
    },
    "gemini": {
      "command": "npx",
      "args": ["-y", "@anthropic/gemini-mcp-server"],
      "env": { "GOOGLE_API_KEY": "AIza..." }
    }
  }
}
```

At `/start`, Claude checks which MCP servers are reachable and flags any that are down. Unavailable integrations don't block the session — Claude notes them and continues.
