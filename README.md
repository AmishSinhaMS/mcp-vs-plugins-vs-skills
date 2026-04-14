<p align="center">
  <h1 align="center">MCP vs Plugins vs skills for Agents:<br>The Path to Native AI Agentic Development</h1>
</p>

<p align="center">
  <em>Understanding the three extensibility models that power modern AI agents — and when to use each</em>
</p>

<p align="center">
  <a href="blog/mcp-vs-plugins-vs-skills.md"><img src="https://img.shields.io/badge/📖_Read_the_Blog_Post-blue?style=for-the-badge" alt="Read Blog"></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/MCP-00A67E?style=flat-square" alt="MCP">
  <img src="https://img.shields.io/badge/Plugins-8B5CF6?style=flat-square" alt="Plugins">
  <img src="https://img.shields.io/badge/AI_Skills-F59E0B?style=flat-square" alt="Skills">
  <img src="https://img.shields.io/badge/Agentic_AI-2026-purple?style=flat-square" alt="2026">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License">
</p>

---

## The Question

Every AI agent needs to connect to external tools, data, and services. But the *how* has fractured into three competing extensibility models:

> **MCP servers** expose tools via a universal protocol.
> **Plugins** package instructions, skills, and agents into installable bundles.
> **AI Skills** encode domain expertise as structured prompts that shape agent behavior.

These are not interchangeable. Each solves a different layer of the agent extensibility problem. This post explains what each one does, where they overlap, and how they compose into a complete agentic architecture.

---

## What's Inside

| File | Description |
|------|-------------|
| [`blog/mcp-vs-plugins-vs-skills.md`](blog/mcp-vs-plugins-vs-skills.md) | The full blog post (~4,000 words) |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | How to contribute |
| [`LICENSE`](LICENSE) | MIT License |

---

## Quick Summary

```
┌─────────────────────────────────────────────────────────────┐
│  Layer 3: SKILLS    "What the agent knows"                  │
│  ─────────────────────────────────────────────────────       │
│  Domain expertise, workflows, best practices                 │
│  Format: Markdown prompt files                              │
│  Example: "How to create a Power BI semantic model"          │
├─────────────────────────────────────────────────────────────┤
│  Layer 2: PLUGINS   "What the agent ships with"             │
│  ─────────────────────────────────────────────────────       │
│  Installable bundles of skills, agents, hooks, MCP configs   │
│  Format: Package with manifest (plugin.json)                │
│  Example: power-bi-agentic-development marketplace           │
├─────────────────────────────────────────────────────────────┤
│  Layer 1: MCP       "What the agent can reach"              │
│  ─────────────────────────────────────────────────────       │
│  Universal tool protocol — live API/data connections         │
│  Format: JSON-RPC over stdio/HTTP                           │
│  Example: GitHub MCP server, Fabric REST endpoints           │
└─────────────────────────────────────────────────────────────┘
```

---

## Key Takeaways

| Model | What It Is | When to Use It |
|-------|-----------|---------------|
| **MCP** | Protocol for tools | When an agent needs to *do something* — call APIs, query databases, manage resources |
| **Plugins** | Packaging for distribution | When you want to *share and install* a collection of capabilities across teams |
| **Skills** | Domain knowledge as prompts | When an agent needs to *know how* — best practices, workflows, format rules |

**👉 [Read the full post →](blog/mcp-vs-plugins-vs-skills.md)**

---

## The Landscape (April 2026)

### MCP Servers

| Server | Maintainer | Capabilities |
|--------|-----------|-------------|
| [GitHub MCP](https://github.com/github/github-mcp-server) | GitHub | Repos, PRs, issues, Actions, code search |
| [Fabric REST APIs](https://learn.microsoft.com/rest/api/fabric/) | Microsoft | Workspaces, lakehouses, semantic models |
| [Kusto/KQL](https://learn.microsoft.com/azure/data-explorer/) | Microsoft | Eventhouse queries, table management |
| [Power BI Query](https://learn.microsoft.com/rest/api/power-bi/) | Microsoft | DAX execution against semantic models |

### Plugin Marketplaces

| Marketplace | Focus | Installable Plugins |
|-------------|-------|-------------------|
| [skills-for-fabric](https://github.com/microsoft/skills-for-fabric) | Microsoft Fabric | 30+ skills across 12 domains |
| [databricks-agent-skills](https://github.com/databricks/databricks-agent-skills) | Databricks | 7 skills |

### Skill Examples

| Skill | Domain | What It Teaches the Agent |
|-------|--------|--------------------------|
| Power-BI-Visuals | Power BI | PBIR visual types, data binding syntax, bulk operations |
| Power-BI-Themes | Power BI | Theme JSON structure, conditional formatting rules |
| e2e-medallion-architecture | Fabric | Bronze/Silver/Gold lakehouse patterns with PySpark |
| pbir-format | PBIP | Visual.json schema, field references, Aggregation patterns |

---

## Contributing

The agent extensibility landscape evolves fast. Contributions, corrections, and new perspectives are welcome. See [`CONTRIBUTING.md`](CONTRIBUTING.md).

---

## License

[MIT](LICENSE)

---

<p align="center">
  <em>All guides are written from personal experience and may need adjustments for your specific environment.</em>
</p>
