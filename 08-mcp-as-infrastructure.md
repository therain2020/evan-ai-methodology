# 08 — MCP as Infrastructure

## What MCP Enables

Model Context Protocol (MCP) servers give Claude Code access to external tools — databases, browsers, search engines, diagram renderers. Without MCP, Claude is a text processor. With MCP, Claude can:

- Query your production database to verify a schema change
- Open a browser and test a user flow end-to-end
- Search the web for the latest API documentation
- Generate a PlantUML diagram and save it as SVG

Each MCP server is a subprocess. Claude auto-discovers their capabilities and calls them as tools. This means your AI goes from "I think the schema looks like X" to "I queried the database and confirmed the schema is X."

## Server Selection Criteria

Evan's 8 MCP servers were chosen by:

1. **Does it enable a workflow I actually use?** Not "is it cool" but "will I invoke it at least weekly."
2. **Is it read-only by default?** Write access requires explicit justification. MySQL is SELECT-only. Playwright is sandboxed.
3. **Is the overhead acceptable?** Each server is a subprocess. Startup time matters.
4. **Is there a maintained package?** npm packages with active communities > bespoke scripts.

## Server-by-Server Rationale

### Tavily — Web Search
**Why:** Claude's training cutoff means it can't answer questions about recent library versions, new APIs, or current events. Tavily fills that gap.
**When:** Research tasks, checking if a library has a known bug, finding documentation for a new version.

### Semantic Scholar — Academic Papers
**Why:** Thesis writing requires finding and citing papers. Semantic Scholar provides structured paper metadata (title, authors, year, citations) that's easier to work with than raw web search results.
**When:** Literature review, finding papers that cite a specific paper, checking an author's publication history.

### PlantUML — Sequence Diagrams
**Why:** Sequence diagrams are the hardest diagram type to get right in a general-purpose drawing tool. PlantUML's text-to-diagram approach means Claude can generate them directly from code.
**When:** API call sequences, authentication flows, database transaction timelines.

### Mermaid — Flowcharts & Gantt
**Why:** Complement to PlantUML. Mermaid's flowchart syntax is simpler for non-sequence diagrams. Gantt charts for project timelines.
**When:** Algorithm flowcharts, project schedules, state machines.

### MySQL — Read-Only Queries
**Why:** During module audits, Claude needs to verify that entity fields match actual database columns. Without MySQL MCP, this is guesswork. With it, every audit includes a schema cross-validation step.
**Security:** Read-only (SELECT + DESCRIBE only). No INSERT, UPDATE, DELETE, DROP.
**When:** Module audits, debugging schema-related bugs, verifying migrations ran correctly.

### Playwright — Headless Browser
**Why:** QA testing without Playwright MCP requires a human to manually click through flows. With it, `/qa` can automate the entire test → screenshot → fix → re-test loop.
**When:** UI testing, visual regression checks, responsive layout verification.

### DrawIO — Architecture Diagrams
**Why:** Architecture diagrams need manual layout tweaking. DrawIO's XML-based format means Claude can generate the structure and the user can fine-tune in the DrawIO editor.
**When:** System architecture diagrams, ER diagrams, deployment diagrams.

### bb-browser — Social/Web Search
**Why:** Research tasks that span multiple platforms (search for a topic on 小红书, 推特, V2EX simultaneously). The 36-platform coverage means one tool instead of 36 browser tabs.
**When:** Competitive analysis, finding how other projects solved a similar problem, Chinese-language tech community research.

## Configuration Principles

1. **API keys as environment variables, never hardcoded.** The `mcp.json` references `$ENV_VAR`, not raw keys.
2. **One server, one purpose.** Don't overload a server with multiple unrelated capabilities.
3. **Restrict output directories.** PlantUML's `PLANTUML_ALLOWED_DIRS` prevents diagram generation from writing arbitrary files.
4. **Sandbox browsers.** Playwright's `--isolated` and `--no-sandbox` flags prevent cross-session contamination.

## Adding a New MCP Server

Checklist before adding:
- [ ] Is there an npm package or maintained binary?
- [ ] Does it run as a subprocess (no persistent daemon)?
- [ ] Is its scope clearly bounded (not a general-purpose shell)?
- [ ] Are its tools read-only or explicitly justified as write?
- [ ] Does it respect the `allowed-tools` whitelist in relevant skills?
