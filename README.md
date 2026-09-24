# Technical Implementation Skill

An AI-assisted process for creating **Technical Implementation (TI) documents** — pre-development design documents covering solution concept, key decisions, risks, and rollout plans.

Works with agent hosts that natively support the complete workflow: interactive approval checkpoints, project file access, codebase research, independent subagents, and external integrations when publishing is requested. The skill does not provide a reduced workflow for hosts missing these capabilities.

## What it does

The skill walks an AI assistant through a structured process:

1. **Data Collection & Research** — gather requirements, study the codebase, discuss approach
2. **Solution Concept** — formulate and approve the core design thesis-by-thesis
3. **Risk & Release Analysis** — Security, Performance, Release & Rollback assessments via dedicated subagents
4. **Deepen** — one-time deep analysis for potential flow improvements beyond the original scope
5. **Publishing** — publish the document to a task tracker or knowledge base

The AI acts as a guide, not a decision maker — it proposes options with trade-offs, you make the choices.

## Repository Contents

```
SKILL.md           # The process guide and Agent Skill entry point
standard.md        # TI document standard (customizable per project)
agents/openai.yaml # Codex UI metadata
README.md          # This file
```

## Installation

### Step 1. Copy files to your project

```bash
# Copy the standard
mkdir -p _tech-implementation
cp path/to/tech-implementation-skill/standard.md _tech-implementation/standard.md

# Copy the skill entry point (location depends on your AI tool — see below)
```

This project copy is optional: if it is absent, the skill uses the Built-in Standard Reference in `SKILL.md`.

### Step 2. Install for your AI tool

<details>
<summary><b>Claude Code</b></summary>

```bash
cp path/to/tech-implementation-skill/SKILL.md \
  .claude/skills/tech-implementation.md
```

Optionally add to your `CLAUDE.md`:

```markdown
- `tech-implementation` — Create a Technical Implementation document: solution concept, key decisions, risks, rollout plan.
```

Invoke with `/tech-implementation` or ask naturally: *"Create a TI for task PROJ-1234"*

</details>

<details>
<summary><b>Codex</b></summary>

Install the skill for the current project:

```bash
mkdir -p .agents/skills/tech-implementation/agents
cp path/to/tech-implementation-skill/SKILL.md \
  .agents/skills/tech-implementation/SKILL.md
cp path/to/tech-implementation-skill/agents/openai.yaml \
  .agents/skills/tech-implementation/agents/openai.yaml
```

For a personal installation available in every project, use `~/.agents/skills/tech-implementation` instead of `.agents/skills/tech-implementation`.

The skill requires Codex subagents. Ensure they are enabled in `~/.codex/config.toml`:

```toml
[agents]
enabled = true
```

Use `/skills` to confirm discovery. Invoke the skill explicitly with:

```
$tech-implementation Create a TI for task PROJ-1234
```

Codex can also invoke it automatically when the request matches the description in `SKILL.md`.

</details>

Other hosts are compatible only when they natively support the complete workflow defined in `SKILL.md`. No host-specific adapters or reduced fallbacks are provided.

## Usage

Ask your AI assistant:

```
Create a TI for task PROJ-1234
Design a technical implementation for the new payment flow
Write a technical implementation for adding SSO support
```

Provide the task context:

- A link to a task in your tracker (Jira, Linear, GitHub Issues, etc.)
- A document or article link
- A local file
- Or just describe the task verbally

## Configuration

Defaults work out of the box. Override in your project's AI configuration:

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `standard_path` | `_tech-implementation/standard.md` | Path to your TI document standard |
| `output_dir` | `_tech-implementation/tasks/` | Where generated TI documents are saved |

### Customizing the standard

The `standard.md` file defines the structure of the output document. Customize it for your project:

- **Add sections** — the skill will pick them up
- **Remove sections** — if you remove Decision Matrix, Security, Performance, or Release & Rollback, the corresponding analysis stage is automatically skipped
- **Modify formatting rules** — the skill follows whatever formatting guidance the standard prescribes

## How it works

### Subagents

Steps marked with **[subagent]** in `SKILL.md` are delegated to separate AI instances using the strongest reasoning model available to the host — Opus for Anthropic, Sol for OpenAI, Gemini Pro for Google, or the equivalent top-tier model of another provider. They enable parallel execution and deeper analysis without overloading the main conversation context. Lightweight agents optimized primarily for speed or cost must not be used for these steps.

Subagent-delegated analysis:

- Security risk assessment
- Performance risk assessment
- Release & rollback analysis
- Deep improvement analysis (Deepen stage)
- Individual proposal validation

### Publishing

After the document is ready, the skill offers to publish it via available external tool integrations (Jira, Linear, Confluence, Notion, etc.). If running as a subagent or in an automated pipeline, publishing is skipped.

### Language

Dialog and document are written in the user's language, auto-detected from the conversation.

## License

MIT
