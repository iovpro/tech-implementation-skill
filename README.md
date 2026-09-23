# Technical Implementation Skill

An AI-assisted process for creating **Technical Implementation (TI) documents** — pre-development design documents covering solution concept, key decisions, risks, and rollout plans.

Works with any AI coding assistant: Claude Code, Cursor, Windsurf, GitHub Copilot, Cline, Aider, or any LLM that accepts instruction files.

## What it does

The guide walks an AI assistant through a structured process:

1. **Data Collection & Research** — gather requirements, study the codebase, discuss approach
2. **Solution Concept** — formulate and approve the core design thesis-by-thesis
3. **Risk & Release Analysis** — Security, Performance, Release & Rollback assessments via dedicated subagents
4. **Deepen** — one-time deep analysis for potential flow improvements beyond the original scope
5. **Publishing** — publish the document to a task tracker or knowledge base

The AI acts as a guide, not a decision maker — it proposes options with trade-offs, you make the choices.

## Repository Contents

```
guide.md       # The process guide (LLM-agnostic instructions)
standard.md    # TI document standard (customizable per project)
README.md      # This file
```

## Installation

### Step 1. Copy files to your project

```bash
# Copy the standard
mkdir -p _tech-implementation
cp path/to/tech-implementation-skill/standard.md _tech-implementation/standard.md

# Copy the guide (location depends on your AI tool — see below)
```

### Step 2. Install for your AI tool

<details>
<summary><b>Claude Code</b></summary>

```bash
mkdir -p .claude/skills
cp path/to/tech-implementation-skill/guide.md .claude/skills/tech-implementation.md
```

Optionally add to your `CLAUDE.md`:
```markdown
- `tech-implementation` — Create a Technical Implementation document: solution concept, key decisions, risks, rollout plan.
```

Invoke with `/tech-implementation` or ask naturally: *"Create a TI for task PROJ-1234"*

</details>

<details>
<summary><b>Cursor</b></summary>

Copy the guide to your project rules:

```bash
mkdir -p .cursor/rules
cp path/to/tech-implementation-skill/guide.md .cursor/rules/tech-implementation.mdc
```

Or reference it in your `.cursorrules`:
```
When asked to create a Technical Implementation, follow the process in _tech-implementation/guide.md
```

And place the guide alongside the standard:
```bash
cp path/to/tech-implementation-skill/guide.md _tech-implementation/guide.md
```

</details>

<details>
<summary><b>Windsurf</b></summary>

Add to your `.windsurfrules`:
```
When asked to create a Technical Implementation, follow the process in _tech-implementation/guide.md
```

```bash
cp path/to/tech-implementation-skill/guide.md _tech-implementation/guide.md
```

</details>

<details>
<summary><b>GitHub Copilot</b></summary>

Reference in `.github/copilot-instructions.md`:
```
When asked to create a Technical Implementation, follow the process in _tech-implementation/guide.md
```

```bash
cp path/to/tech-implementation-skill/guide.md _tech-implementation/guide.md
```

</details>

<details>
<summary><b>Cline / Aider / Other</b></summary>

Place the guide in your project and reference it in whatever configuration your tool uses for custom instructions:

```bash
cp path/to/tech-implementation-skill/guide.md _tech-implementation/guide.md
```

Point your tool's instruction configuration at `_tech-implementation/guide.md`.

</details>

<details>
<summary><b>Generic LLM (API / Chat)</b></summary>

Include the contents of `guide.md` in your system prompt or as a context document. The guide is self-contained — it works as a system prompt instruction set for any capable LLM.

</details>

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

- **Add sections** — the guide will pick them up
- **Remove sections** — if you remove Decision Matrix, Security, Performance, or Release & Rollback, the corresponding analysis stage is automatically skipped
- **Modify formatting rules** — the guide follows whatever formatting guidance the standard prescribes

## How it works

### Subagents

Steps marked with **[subagent]** in the guide are delegated to separate AI instances (Opus, Sol, Gemini Pro, or equivalent) for parallel execution and deeper analysis:

Subagent-delegated analysis:
- Security risk assessment
- Performance risk assessment
- Release & rollback analysis
- Deep improvement analysis (Deepen stage)
- Individual proposal validation

### Publishing

After the document is ready, the guide offers to publish it via available external tool integrations (Jira, Linear, Confluence, Notion, etc.). If running as a subagent or in an automated pipeline, publishing is skipped.

### Language

Dialog and document are written in the user's language, auto-detected from the conversation.

## License

MIT
