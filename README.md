# AirOps Content Marketing Plugin

A Claude Code plugin that turns AI into a brand-aware content marketing partner.
Playbooks as skills, subagents for writing and validation, and the AirOps MCP
for live brand context and AEO data.

## What This Is

- **Playbooks as skills** — create-blog, create-comparison, create-listicle, refresh-content are explicit skills.
- **Brand from AirOps MCP** — No local brand files. The agent pulls brand identity, writing guidelines, tone, and rules live from your AirOps Brand Kit via the MCP.
- **Agents** — content-writer and content-critic for brand-voice content. ai-search-analyst and page-analyst for data analysis in separate context windows.
- **AirOps MCP** — The plugin connects to the AirOps MCP server (`https://app.airops.com/mcp`) for brand data, AEO analytics, citations, page metrics, and more.

## How Brand Context Works

There is no injected brand file. All brand context is pulled live from the AirOps MCP:

1. Agent calls `list_brand_kits` to find the active Brand Kit
2. Agent calls `get_brand_kit` with includes (product_lines, competitors, audiences, content_types)
3. The response provides: brand identity, writing guidelines, tone, persona, rules, sample content, visual settings, and targeting

This happens at the start of every task, and again when the content-writer or content-critic agents spin up — so brand voice is always fresh and never stale.

## Plugin Structure

```
.claude-plugin/plugin.json      # Manifest
CLAUDE.md                       # Identity + methodology + AirOps MCP reference
.mcp.json                       # Connects to AirOps + Semrush MCPs
skills/
  create-blog/SKILL.md          # Playbook skills (explicit)
  create-comparison/SKILL.md
  create-listicle/SKILL.md
  refresh-content/SKILL.md
  aeo-data-guide/SKILL.md       # Context guide — data model, metrics, relationships
  seo-guidelines/SKILL.md       # Auto-applied
  content-quality-report/SKILL.md
agents/
  content-writer.md             # Writes content in brand voice
  content-critic.md             # Validates brand alignment
  ai-search-analyst.md          # AI search visibility — prompts, answers, mentions, citations
  page-analyst.md               # Page performance — AEO, GSC, GA4 metrics
README.md
```

## Setup

### Prerequisites

- An AirOps account with a Brand Kit configured
- A Semrush account with API access (SEO Business plan + Standard API, or Trends API)
- Claude Code, Cursor, or another host that supports MCP

### Connect

The plugin's `.mcp.json` connects to both MCP servers:

```json
{
  "mcpServers": {
    "airops": {
      "url": "https://app.airops.com/mcp"
    },
    "semrush": {
      "url": "https://mcp.semrush.com/v1/mcp"
    }
  }
}
```

When the host loads the plugin, it connects to both MCPs. Each authenticates via
OAuth on first use — you'll be redirected to the respective login pages.

### Test locally

```bash
claude --plugin-dir .
```

Then:

```
create-blog "AI tools for content marketing"
create-comparison "HubSpot" "Marketo"
create-listicle "productivity tools" count=10
refresh-content "https://example.com/old-post"
content-quality-report path/to/draft.html "target keyword"
```