# Claude Search Tools

A Claude Code plugin providing tiered web search skills with graceful degradation.

| Tier | Skill | Backend | Use Case |
|:-----|:------|:--------|:---------|
| 1 | Built-in `WebSearch` | Claude | Trivial lookups, quick fact checks |
| 2 | `/search-tools:gemini-search` | Gemini CLI | Docs, API changes, how-tos |
| 3 | `/search-tools:deep-search` | Gemini + Perplexity | Complex research, cross-referenced results |

Both skills degrade gracefully — if a backend is unavailable, they fall back to whatever is available, down to Claude's built-in WebSearch.

## Installation

### Option 1: Via Marketplace (Recommended)

1. Add the marketplace to Claude Code:
```bash
/plugin marketplace add AdamOliver1/claude-search-tools
```

2. Install the plugin:
```bash
/plugin install search-tools@adamoliver1-plugins
```

Or use the interactive UI: `/plugin` → **Discover** tab → search for "search-tools"

### Option 2: Direct GitHub Install

```bash
/plugin install https://github.com/AdamOliver1/claude-search-tools
```

### Option 3: Local Development

Clone and test locally:
```bash
git clone https://github.com/AdamOliver1/claude-search-tools.git
cd claude-search-tools
claude --plugin-dir .
```

## Prerequisites

Both backends are **optional** — skills work with any combination installed.

### Gemini CLI

```bash
npm install -g @google/gemini-cli
gemini  # Run once to authenticate
```

### Perplexity MCP

This plugin bundles a `.mcp.json` config for Perplexity. You just need to set your API key as an environment variable:

```bash
export PERPLEXITY_API_KEY="your-api-key-here"
```

Get an API key at [perplexity.ai/settings/api](https://www.perplexity.ai/settings/api).

**To persist the key**, add it to your shell profile (`~/.zshrc` or `~/.bashrc`):
```bash
echo 'export PERPLEXITY_API_KEY="your-api-key-here"' >> ~/.zshrc
source ~/.zshrc
```

## Usage

### Tier 2 — Gemini Search

```
/search-tools:gemini-search how to configure ESLint flat config
/search-tools:gemini-search --deep compare React Server Components vs Astro Islands
```

**Features:**
- Default model: `gemini-2.5-flash` (fast)
- `--deep` flag: upgrades to `gemini-3-pro-preview`
- Falls back to WebSearch if Gemini CLI is not installed
- Returns cited sources with technical details

### Tier 3 — Deep Search

```
/search-tools:deep-search compare Bun vs Deno vs Node for production APIs
/search-tools:deep-search --deep comprehensive analysis of WebTransport vs WebSockets
```

**Features:**
- Queries Gemini and Perplexity in parallel
- Cross-references results: agreements, unique findings, contradictions
- `--deep` flag: upgrades both backends to their research-grade models
- Gracefully handles missing backends (uses whatever is available)
- Shows which backends were actually used

## Optional: CLAUDE.md Snippet

Add this to your `~/.claude/CLAUDE.md` to give Claude context about when to use each tier:

```markdown
## Web Search: Tiered Search Strategy

Use the following hierarchy when you need to search the web. Choose the appropriate tier based on complexity — do NOT always escalate to higher tiers.

### Tier 1: Built-in WebSearch (Simple/Quick)
For trivial lookups, quick fact checks, or simple one-line answers — use Claude's built-in `WebSearch` tool directly. No skill invocation needed.
- Examples: "what version is X?", "is Y deprecated?", "what's the capital of Z?"

### Tier 2: `/search-tools:gemini-search` (Standard)
For meaningful searches that need more detail — documentation lookups, API changes, framework comparisons, technical how-tos. Claude decides whether to use `--deep` flag or not based on query complexity.
- Examples: "how does X library handle Y?", "latest changes in React 19", "best practices for Z"

### Tier 3: `/search-tools:deep-search` (Complex / Multi-Source)
For complex research requiring high-confidence, cross-referenced results from multiple sources (Gemini + Perplexity in parallel). Use when:
- The user explicitly asks for `/deep-search` or says "deep search"
- The user tells Claude to "use deep search when you need to search"
- The topic is complex and benefits from cross-referencing multiple sources
- High confidence is needed (architectural decisions, comparing technologies, etc.)

### Decision Guide
| Complexity | Tool | Example |
|:---|:---|:---|
| Trivial | `WebSearch` | "what's the latest Node.js LTS version?" |
| Standard | `/search-tools:gemini-search` | "how to configure ESLint flat config" |
| Complex | `/search-tools:deep-search` | "compare Bun vs Deno vs Node for production APIs in 2026" |
| User says so | `/search-tools:deep-search` | user says "use deep search" or "search thoroughly" |
```

## Troubleshooting

### Skills not showing up
- Verify plugin is installed: `/plugin` → **Installed** tab
- Check for errors: `/plugin` → **Errors** tab
- Restart Claude Code if you just installed the plugin

### Gemini CLI not working
- Verify installation: `which gemini`
- Re-authenticate: `gemini` (run the CLI once)
- Check model access: some models require API keys or special access

### Perplexity not working
- Verify API key is set: `echo $PERPLEXITY_API_KEY`
- Check MCP server status in Claude Code
- The skill will automatically fall back to Gemini-only if Perplexity fails

## License

MIT
