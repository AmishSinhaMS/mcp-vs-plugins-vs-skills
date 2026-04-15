# MCP vs Plugins vs Skills for agents: The Path to Native AI Agentic Development

*Understanding the three extensibility models that power modern AI agents — April 2026*

---

## Introduction

Every useful AI agent needs to go beyond its training data. It needs to call APIs, query databases, manage infrastructure, understand domain-specific workflows, and follow organizational best practices. The question is no longer *whether* agents should be extensible — it's *how*.

In 2026, three distinct extensibility models have emerged in the agentic AI ecosystem:

- **Model Context Protocol (MCP)** — a universal protocol for connecting agents to external tools and data sources
- **Plugins** — installable packages that bundle capabilities, instructions, and sub-agents for distribution
- **AI Skills** — structured prompt files that encode domain expertise and shape how agents approach specific tasks

These three models are not competing alternatives. They operate at different layers of the agent architecture, and the most capable agent systems use all three. But the differences between them are poorly understood, leading to confusion about when to use what.

This post breaks down each model, shows how they relate, and provides a practical framework for composing them into a complete agentic architecture.

---

## The Three Layers

Think of agent extensibility as a stack. Each layer answers a different question:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Layer 3: SKILLS         "What does the agent KNOW?"        │
│  Domain expertise, workflows, format rules, best practices  │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Layer 2: PLUGINS        "What does the agent SHIP WITH?"   │
│  Installable bundles: skills + agents + hooks + config      │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Layer 1: MCP SERVERS    "What can the agent REACH?"        │
│  Live connections to APIs, databases, and services          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

Let's examine each layer.

---

## Layer 1: MCP — The Universal Tool Protocol

### What it is

The [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) is an open standard that defines how AI agents discover and invoke external tools. An MCP server exposes a set of capabilities — functions the agent can call — through a structured JSON-RPC interface over stdio or HTTP.

### How it works

```
┌──────────────┐         JSON-RPC          ┌──────────────────┐
│              │ ──────────────────────────▶│                  │
│   AI Agent   │   "list tools"            │   MCP Server     │
│  (Copilot,   │   "call tool X"           │  (GitHub, DB,    │
│   Claude)    │ ◀──────────────────────── │   Fabric, etc.)  │
│              │   tool results             │                  │
└──────────────┘                           └──────────────────┘
```

When an agent starts, it connects to configured MCP servers and discovers their available tools. When the agent decides it needs to use one — say, searching GitHub code or querying a database — it calls the tool through the MCP protocol and receives structured results.

### What MCP is good at

| Strength | Example |
|----------|---------|
| **Live data access** | Query a Fabric lakehouse for current sales figures |
| **Action execution** | Create a GitHub pull request, provision a resource |
| **Universal compatibility** | Same MCP server works with Copilot CLI, Claude Code, SDK agents |
| **Dynamic discovery** | Agent learns available tools at runtime, not compile time |

### What MCP is not

MCP servers provide **tools**, not **knowledge**. An MCP server can expose a function to "execute a DAX query," but it doesn't teach the agent *what* DAX query to write, *when* to use SUMMARIZECOLUMNS vs CALCULATE, or *how* to structure a star schema. That's where skills come in.

### Real-world MCP servers

| Server | What It Provides |
|--------|-----------------|
| **GitHub MCP** | Repository management, PR operations, code search, Actions |
| **Power BI Query** | DAX query execution against semantic models |
| **Kusto REST API** | KQL query execution against Eventhouse databases |
| **PostgreSQL MCP** | SQL query execution, schema discovery |
| **Filesystem MCP** | File read/write/search operations |

---

## Layer 3: Skills — Domain Knowledge as Prompts

### What they are

AI Skills are structured Markdown files that encode domain expertise, workflows, command syntax, and best practices. When loaded into an agent's context, they shape *how* the agent thinks about and approaches a specific domain — without writing any code.

### How they work

A skill is typically a Markdown file with YAML frontmatter that gets injected into the agent's system prompt when the skill is invoked:

```markdown
---
name: Power-BI-Visuals
description: >
  Add, configure, bind data to, and manage visuals on PBIR report pages
  using pbi-cli.
tools: pbi-cli
---

# Power BI Visuals Skill

## Adding Visuals

pbi visual add --page overview --type bar --name sales_bar

## Binding Data

pbi visual bind sales_bar --page overview \
    --category "Product[Category]" --value "Sales[Revenue]"

## Supported Visual Types (32)

| Alias    | PBIR Type        | Bind Options              |
|----------|------------------|---------------------------|
| bar      | barChart         | --category, --value       |
| card     | card             | --field                   |
| table    | tableEx          | --value                   |
| ...      | ...              | ...                       |
```

The agent reads this skill and now *knows* how to create visuals, what command syntax to use, which visual types are available, and how data binding works — all from a prompt file, with zero code.

### What skills are good at

| Strength | Example |
|----------|---------|
| **Domain expertise** | Teach the agent Power BI PBIR format rules |
| **Workflow guidance** | "First create the theme, then add visuals, then bind data" |
| **Format compliance** | Correct JSON schema for visual.json Aggregation fields |
| **Tool command reference** | pbi-cli syntax with all flags and options |
| **Best practices** | "Use Column with Aggregation for fact table fields, not Measure" |

### What skills are not

Skills don't provide **live data or actions**. A skill can teach the agent the correct DAX syntax for time intelligence, but it can't *execute* that DAX query against your semantic model. That requires an MCP server or a CLI tool.

### The knowledge vs execution split

| Need | Solution |
|------|----------|
| Agent needs to *know* how to write a KQL query | **Skill** |
| Agent needs to *run* a KQL query | **MCP server** (or CLI tool) |
| Agent needs to *know* PBIR visual.json schema | **Skill** |
| Agent needs to *create* a visual.json file | **CLI tool** (informed by the skill) |

This is the fundamental insight: **skills provide the knowledge, MCP provides the execution**. Together, they make agents that both understand *what* to do and can actually *do* it.

---

## Layer 2: Plugins — The Distribution Mechanism

### What they are

Plugins are installable packages that bundle skills, sub-agents, hooks, MCP server configurations, and scripts into a distributable unit. They are the *packaging layer* — the mechanism for sharing and installing agent capabilities across teams and communities.

### How they work

A plugin is a directory with a manifest file (`plugin.json` or `package.json`) and a structured collection of files:

```
Fabric-power-bi-agentic-development/
├── .claude-plugin/
│   └── plugin.json          # Manifest: name, version, description
├── plugins/
│   ├── reports/
│   │   ├── agents/          # Sub-agents (deneb-reviewer, svg-reviewer)
│   │   └── skills/          # Skill files for report development
│   ├── pbip/
│   │   ├── agents/          # pbip-validator agent
│   │   ├── skills/          # PBIR format, TMDL syntax, rename cascades
│   │   │   └── examples/    # Reference visual.json templates
│   │   └── hooks/           # PostToolUse validation hooks
│   ├── semantic-models/
│   │   └── agents/          # semantic-model-auditor
│   ├── pbi-desktop/
│   │   ├── agents/          # query-listener (capture live DAX)
│   │   └── skills/          # Desktop connection, TOM/ADOMD
│   └── tabular-editor/
│       ├── agents/          # BPA expression helper
│       └── scripts/         # C# scripts for Tabular Editor
└── README.md
```

Install a plugin, and your agent gains:
- **Skills** that teach it domain-specific knowledge
- **Sub-agents** that handle specialized review or validation tasks
- **Hooks** that run validation after file operations
- **Reference examples** that provide correct templates for complex formats

### What plugins are good at

| Strength | Example |
|----------|---------|
| **Distribution** | `copilot plugin skills-for-fabric` |
| **Bundling** | One install gives you 6 sub-plugins, each with skills, agents, and hooks |
| **Versioning** | Plugin marketplace tracks versions, changelogs, auto-updates |
| **Community** | Anyone can publish a plugin to a marketplace |
| **Composition** | Plugin A's skills + Plugin B's MCP servers = comprehensive agent |

### What plugins are not

Plugins are a **delivery mechanism**, not a runtime. A plugin doesn't run tools or maintain connections — it provides the files and configurations that shape agent behavior. The actual execution still happens through MCP servers, CLI tools, and the agent's built-in capabilities.

---

## How the Three Layers Compose

In practice, all three layers work together. Here's how they compose in a real scenario:

### Example: Building a Fabric/Power BI Report

```
User: "Create a sales dashboard from my Fabric semantic model"

┌─────────────────────────────────────────────────────────────┐
│  PLUGIN (installed at setup time)                           │
│  Fabric-power-bi-agentic-development                               │
│                                                             │
│  Provides:                                                  │
│  ├── SKILL: pbir-format                                     │
│  │   → Teaches visual.json schema, Aggregation syntax       │
│  │   → Reference examples for each visual type              │
│  ├── SKILL: Power-BI-Visuals                                │
│  │   → pbi-cli commands for adding/binding visuals          │
│  ├── AGENT: pbip-validator                                  │
│  │   → Validates the report structure after creation        │
│  └── HOOK: validate-pbir                                    │
│      → Auto-validates JSON after each file write            │
└─────────────────────┬───────────────────────────────────────┘
                      │ loaded into agent context
                      ▼
┌─────────────────────────────────────────────────────────────┐
│  SKILL (active during this task)                            │
│  Fabric-Power-BI-Visuals + pbir-format                             │
│                                                             │
│  Agent now KNOWS:                                           │
│  • 32 visual types and their bind options                   │
│  • Column vs Measure vs Aggregation field patterns          │
│  • Correct queryState roles (Category, Y, Values, Group)    │
│  • How to use pbi-cli with --no-sync for batching           │
└─────────────────────┬───────────────────────────────────────┘
                      │ agent plans and executes
                      ▼
┌─────────────────────────────────────────────────────────────┐
│  MCP + CLI TOOLS (called at runtime)                        │
│                                                             │
│  Agent DOES:                                                │
│  • Power BI Query MCP → Run DAX to discover model tables    │
│  • pbi-cli → Scaffold report, add pages, create visuals     │
│  • Fabric REST API → Publish report to workspace            │
│  • GitHub MCP → Commit and push to repository               │
└─────────────────────────────────────────────────────────────┘
```

**Plugin** delivers the knowledge and validation tooling. **Skill** shapes the agent's approach in real-time. **MCP** connects the agent to live systems for execution.

---

## Comparison Matrix

| Dimension | MCP Server | Plugin | AI Skill |
|-----------|-----------|--------|----------|
| **Layer** | Execution (L1) | Distribution (L2) | Knowledge (L3) |
| **What it provides** | Tools and actions | Installable capability bundles | Domain expertise |
| **Format** | JSON-RPC server (code) | Directory with manifest | Markdown files |
| **When loaded** | Agent startup | Installation time | Per-task invocation |
| **Runtime behavior** | Agent calls tools | None (static files) | Injected into prompt |
| **Requires code?** | Yes (server implementation) | No (files only) | No (Markdown only) |
| **Who builds them** | Platform engineers | Domain experts + devs | Anyone with expertise |
| **Cross-agent?** | ✅ Universal (MCP standard) | ⚠️ Agent-specific packaging | ⚠️ Format varies |
| **Examples** | GitHub MCP, Postgres MCP | skills-for-fabric | Power-BI-Visuals, pbir-format |

---

## Decision Framework

### When to build an MCP server

Build an MCP server when you need agents to **interact with a live system** — an API, a database, a cloud service, or any stateful resource.

**Signals:**
- Your tool needs to make HTTP calls, run queries, or manage resources
- You want the tool to work across different agent platforms (Copilot, Claude, SDK)
- The tool's capabilities should be discovered dynamically at runtime
- Other teams or the community would benefit from the same integration

### When to build a plugin

Build a plugin when you want to **distribute a collection of capabilities** — skills, agents, hooks, and configurations — as a single installable unit.

**Signals:**
- You have multiple related skills that should be installed together
- You want versioning, changelogs, and update mechanisms
- You're building for a community (open-source marketplace)
- Your package includes sub-agents, validation hooks, or reference examples
- You want other teams to get your agent capabilities with one command

### When to build a skill

Build a skill when you need to **teach an agent domain expertise** — workflows, syntax, best practices, or format rules — without writing any code.

**Signals:**
- The knowledge is about *how* to do something, not *executing* something
- You want the agent to follow specific patterns or conventions
- The domain has complex rules that the base model doesn't reliably know
- You need the agent to use specific command syntax with correct flags
- A subject-matter expert (not necessarily a programmer) can author the content

---

## The Maturity Path

Teams typically adopt these extensibility models in a predictable sequence:

| Stage | What Happens | Extensibility Used |
|-------|-------------|-------------------|
| **1. Vanilla agent** | Use Copilot/Claude out of the box | None |
| **2. Custom instructions** | Add CLAUDE.md or copilot-instructions.md | Proto-skills |
| **3. MCP connections** | Connect to GitHub, databases, cloud APIs | MCP |
| **4. Skill authoring** | Write domain-specific skill files | Skills |
| **5. Plugin consumption** | Install community plugins (data-goblin, skills-for-fabric) | Plugins + Skills |
| **6. Plugin authoring** | Package and publish your own plugins | All three |
| **7. Platform integration** | MCP servers feed SDK agents in production | MCP + SDK + Skills |

Most teams in early 2026 are between stages 2 and 5. The fastest path to agent effectiveness is installing existing plugins (stage 5) while gradually authoring custom skills for your specific domain (stage 4).

---

## Anti-Patterns to Avoid

### 1. Building an MCP server when a skill would suffice

If the agent just needs to know *how* to use an existing CLI tool, write a skill — don't wrap the CLI in an MCP server. Skills are simpler, faster to author, and don't require running a server process.

**❌** Build an MCP server that wraps `pbi-cli` commands
**✅** Write a skill that documents `pbi-cli` syntax and patterns

### 2. Putting execution logic in skills

Skills should contain knowledge, not code. If your "skill" file includes shell scripts or API calls, it belongs in an MCP server or a hook.

**❌** Skill that says "run this PowerShell script to create a table"
**✅** Skill that teaches the correct table schema + MCP server that executes creation

### 3. Distributing a single skill as a plugin

Plugins add overhead (manifest, versioning, marketplace registration). If you have one skill file, just add it to your repository's instruction files. Use plugins when you have a meaningful bundle.

**❌** Plugin with one skill and nothing else
**✅** Plugin with skills + agents + hooks + reference examples for a domain

### 4. Ignoring the existing ecosystem

Before building custom MCP servers or skills, check whether the community has already built what you need. The ecosystem is growing fast.

---

## Looking Ahead

The three-layer model is still evolving. Several trends are shaping its future:

**MCP is becoming the standard.** Microsoft Foundry, OpenAI, Anthropic, and the open-source community are all converging on MCP as the tool protocol. Building on MCP today is a safe bet for cross-platform compatibility.

**Skills are getting smarter.** Today, skills are static Markdown files. Tomorrow, they may be dynamically composed based on the task context — pulling relevant sections from a skill library rather than loading entire files.

**Plugins are consolidating.** The early fragmentation of plugin formats across Claude Code, Copilot CLI, and other tools is giving way to shared standards. The investment in creating a plugin is increasingly paying off across multiple agent platforms.

**The lines are blurring.** Some plugins now include MCP server configurations. Some MCP servers ship with companion skills. The future likely involves tighter integration between all three layers, with the packaging becoming invisible to the end user.

---

## Conclusion

MCP, Plugins, and Skills are three complementary layers of agent extensibility:

| Layer | Purpose | Built by | Analogy |
|-------|---------|----------|---------|
| **MCP** | Execution — what the agent can do | Engineers | API / driver |
| **Plugins** | Distribution — how capabilities are shared | Community | Package manager |
| **Skills** | Knowledge — what the agent knows | Domain experts | Documentation |

The most effective agent systems use all three:
- **MCP** connects agents to live systems and APIs
- **Skills** teach agents domain expertise and best practices
- **Plugins** package everything into installable, shareable bundles

Start by installing existing plugins for your domain. Layer in MCP connections for your proprietary systems. Author skills for your team's specific workflows and conventions.

The path to native agentic development isn't choosing between these models — it's composing them.

---

## Resources

| Resource | Link |
|----------|------|
| Model Context Protocol Specification | [modelcontextprotocol.io](https://modelcontextprotocol.io/) |
| MCP Server Registry | [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) |
| GitHub Copilot CLI (Plugins & Skills) | [docs.github.com/copilot](https://docs.github.com/copilot/concepts/agents/about-copilot-cli) |
| Skills for Fabric Marketplace | [github.com/microsoft/skills-for-fabric](https://github.com/microsoft/skills-for-fabric) |
| Power BI Agentic Development | [github.com/data-goblin/power-bi-agentic-development](https://github.com/data-goblin/power-bi-agentic-development) |
| Databricks Agent Skills | [github.com/databricks/databricks-agent-skills](https://github.com/databricks/databricks-agent-skills) |
| Microsoft Foundry Agent Service | [learn.microsoft.com/azure/ai-services/agents](https://learn.microsoft.com/azure/ai-services/agents/) |
| Semantic Kernel Plugins | [github.com/microsoft/semantic-kernel](https://github.com/microsoft/semantic-kernel) |

---

*All guides are written from personal experience and may need adjustments for your specific environment.*

*Last updated: April 2026*
