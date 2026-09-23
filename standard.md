# Technical Implementation Document Standard

## Purpose

A Technical Implementation (TI) document describes the **solution concept** for a task before development begins.

**Primary goal:** align the chosen approach with the team lead / manager / stakeholders — prevent costly rework during or after development.

**Secondary goals:**

- Estimation and timeline planning
- Risk identification and validation

## Principles

### Required

- Describe the **concept** of the solution in bullet points, not implementation details
- Mention only key fields that play a role in logic
- Every section (Security, Performance, Release & Rollback) is filled **always**, even when no risks exist — with justification for why there are no risks

### Prohibited

- Full contracts and data schemas (Entity, DTO, JSON contracts) — for both internal and external communication
- Duplicating business logic already described in requirements
- Implementation-level pseudocode (line-by-line changes, file:line)
- Artificially inflating volume (including AI-generated filler content)

### Acceptable

- **Diagrams** — when they optimize understanding and replace textual description (not duplicate it). The reviewer may request a diagram if needed
- **Key fields** — specific fields that determine logic branching or system behavior

## Document Sections

### 1. Introduction (required)

Brief description of the task in 2–3 sentences: what we're doing and why.

### 2. Solution Concept (required)

The main section. Describes **what** we're doing and **why** this approach was chosen.

**Contains:**

- Brief description of the solution
- Interaction diagrams between services/modules (when needed)
- Key fields affecting logic (without full schemas)
- Main data flows

**Formatting:**

- Split into subsections (`###`) by semantic blocks (problem, approach, routing, etc.)
- Prefer lists and structural elements over continuous text. If a paragraph contains multiple ideas — break into separate points
- Format connections between components (routes, conditions, dependencies) as lists, not prose
- Start each semantic block with the key thesis in **bold**, followed by elaboration
- Technical identifiers (service names, methods, fields) — in `code` format

**Does not contain:**

- Full Entity/DTO definitions
- Detailed pseudocode
- Repetition of business requirements

### 3. Decision Matrix (when alternatives exist)

Used when there are multiple implementation options and the choice needs justification.

Records all **important** decisions — not only those requiring management approval, but any decisions where alternatives were seriously considered and a deliberate choice was made. Trivial or obvious choices should not be included.

For each decision, document:
- What was decided
- What was chosen
- What alternatives were considered and why they were rejected
- Why this particular choice was made

**Format:**

| Decision | Choice | Alternatives Considered | Rationale |
| -------- | ------ | ----------------------- | --------- |
| _What was decided_ | _What was chosen_ | _What was rejected and why_ | _Why this choice_ |

### 4. Security (required)

Description of security risks and how they are mitigated — conceptually.

- What threats the implementation carries
- How we protect against them

**If no risks** — explicitly state and justify: why the task carries no security risks.

### 5. Performance (required)

Description of performance risks and how they are mitigated — conceptually.

- What could affect performance
- How we prevent / optimize

**If no risks** — explicitly state and justify: why the task carries no performance risks.

### 6. Release & Rollback (required)

Feature release specifics and action plan for problems — conceptually.

- Rollout specifics (feature flags, migrations, inter-service dependencies)
- What to do if something goes wrong
- How to roll back the release

**If no specifics** — explicitly state and justify: why the release carries no risks and rollback is trivial.

## Anti-patterns

| What | Why it's bad |
| ---- | ------------ |
| Full Entity/DTO schemas with every field | Bloats document, instantly outdated, duplicates code |
| Repeating text from business requirements | Duplication → desync → confusion |
| Line-by-line changes (file:line, code diffs) | Implementation details, not concept. Outdated on refactoring |
| Diagram + text about the same thing | Duplication. Diagram replaces text, doesn't supplement it |
| AI-generated content for volume | Reduces quality, complicates review |
| Empty Security/Performance/Release section | Always fill — at minimum "no risks" with justification |

## Template

```markdown
# Technical Implementation: [Task Name]

**Task:** [TASK-ID](link)

## Introduction

_Brief description of the task in 2–3 sentences: what we're doing and why._

## Solution Concept

_What we're doing and why. Interaction diagrams. Key fields._

## Decision Matrix

| Decision | Choice | Alternatives Considered | Rationale |
| -------- | ------ | ----------------------- | --------- |
|          |        |                         |           |

## Security

_Security risks and how we mitigate. Or: "No risks because..."_

## Performance

_Performance risks and how we mitigate. Or: "No risks because..."_

## Release & Rollback

_Release specifics. Rollback plan. Or: "Standard release, trivial rollback because..."_
```
