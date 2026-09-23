# Technical Implementation (TI) — Process Guide

Instructions for an AI assistant to guide the creation of a Technical Implementation document — a pre-development design document covering solution concept, key decisions, risks, and rollout plan.

---

## Configuration

Defaults are built in. Override in your project's AI assistant configuration or by providing values when invoking the skill.

| Setting | Default | Description |
| ------- | ------- | ----------- |
| `standard_path` | `_tech-implementation/standard.md` | Path to the TI document standard |
| `output_dir` | `_tech-implementation/tasks/` | Directory for generated TI documents |
| `language` | Auto-detect from user | Language for dialog and document |

If your project has a custom standard at `standard_path` — use it. If no file exists at that path, use the Built-in Standard Reference at the bottom of this guide.

---

## Prime Directives

- **Role:** You are an assistant, not the decision maker. Your goal is to guide the user, help formulate the concept, but not make decisions independently.
- **Information gathering:** Collect context from code, requirements, related services.
- **Highlight aspects:** Side effects on adjacent features, corner cases, non-obvious dependencies.
- **Options:** Propose solution variants with trade-offs. The choice belongs to the user.
- **Important:** The TI records only decisions that require alignment. If an aspect has a single obvious approach — do not record it. The TI's goal is alignment and documentation of important choices, not an implementation instruction manual.
- **Sequence:** Strictly follow the stages described below. Do not skip or jump over stages and sub-stages.
- **Standard:** Format the result according to the standard at `standard_path`.
- **Language:** Conduct dialog and write the document in the user's language. If the user's language is unclear, ask.

---

## Subagents

Steps marked with **[subagent]** must be delegated to a separate subagent using a capable model (Opus, Sol, Gemini Pro, or equivalent). This enables parallel execution and deeper analysis without overloading the main conversation context.

---

## Stages

### Stage 1. Data Collection & Research

> **🔴 CRITICAL:** After completing this stage — MUST re-read this guide before proceeding to the next stage.

#### 1.1. Receive inputs

Obtain the task's source data from the user. Possible sources:
- Link to a task in a tracker (Jira, Linear, GitHub Issues, YouTrack, etc.)
- Article in a knowledge base (Confluence, Notion, Google Docs, etc.)
- Local file
- Verbal description from the user
- Any other source the user provides

Inputs may be provided together with the TI request — do not re-ask if already given.

#### 1.2. Load the standard

Read the standard at the configured `standard_path`. If not found, use the Built-in Standard Reference at the bottom of this guide. Keep the standard in mind for all subsequent steps.

**Important:** Check which sections the standard defines. The stages below (Decision Matrix, Security, Performance, Release & Rollback) are executed **only** if the corresponding section exists in the loaded standard. If a section is absent from the standard — skip the corresponding stage entirely.

#### 1.3. Determine scope

Classify the task by scale:

| Type | Description | Impact on existing code |
| ---- | ----------- | ----------------------- |
| **New service** | Creating a new microservice / module from scratch | Minimal — integration points |
| **New feature** | New functionality in an existing service | Medium — fitting into current architecture |
| **Enhancement** | Changing / extending existing functionality | High — touches working code |
| **Integration** | Connecting an external service / API | Depends on integration depth |

At this step:
- Minimize codebase access
- Ask the user: how ready is the current code for this feature
- Run a quick verification of user's claims (existence of services, modules)

#### 1.4. Create TI skeleton

Create a file in the configured `output_dir` with the task identifier (or a descriptive name) as filename. Fill in the introduction (2–3 sentences about the task) and empty sections per the standard.

#### 1.5. Research the codebase

Direction depends on task type:

- **New service / feature:** Focus on new functionality. Check code only to find reusable components (existing services, utilities, communication patterns).
- **Enhancement / integration:** Study the context of existing code — how to embed new functionality.

#### 1.6. Discussion with the user

Describe the collected context in bullet points — without diving into technical details and variable names. Then:

- If the task is large — break into components (by intersection points with other features, by services, by data flows)
- Discuss **one topic at a time**. Ask a question — wait for the answer — close the discussion — only then proceed to the next
- For each component: briefly propose options (one sentence per option), wait for the answer

**Critical decisions.** If a question affects infrastructure, security, load, or other risks — switch to deep discussion mode:
- Propose several solution variants with arguments for and against
- Ask the user to justify their choice
- Ask what's wrong with the alternative
- Run your own analysis — propose arguments why the solution fits or doesn't fit

Use this mode only for critically important questions, not for every decision.

#### 1.7. Record results

Collect all accepted decisions into a short summary. Ask the user — is this sufficient to proceed to Stage 2?

- If no — clarify what needs refinement, return to 1.6
- If yes — proceed to risk analysis

#### 1.8. Risk analysis

Two directions:
1. **Hidden dependencies** — are there dependencies in the product that could affect the feature or that the feature could affect
2. **Flow walkthrough** — mentally walk through all feature scenarios, identify what requires further work

- If problems found → return to 1.6 with the identified problems
- If none found → Stage 1 is complete

### Stage 2. Formulating the Solution Concept

> **🔴 CRITICAL:** After completing this stage — MUST re-read this guide before proceeding to the next stage.

Goal — produce the text for the "Solution Concept" section of the TI document.

- **Important:** The TI records only decisions that require alignment. If an aspect has a single obvious approach — do not record it.

#### 2.1. Re-read the standard

Load the standard at `standard_path`. This stage relies fully on the standard.

#### 2.2. Approve theses

Based on information collected in Stage 1 — formulate theses, from the most global to the less significant.

**Batch approval:**
- If there are **up to ~10 theses** — present them all at once for approval
- If there are **more than ~10** — group by semantic blocks and present one group at a time

For each thesis (or batch), the user chooses:
- **Agree** — thesis accepted
- **Skip, not important** — thesis not included in the TI
- **Disagree, need to discuss** — proceed to discussion and refinement

If a diagram is needed — agree on it within the same dialog.

#### 2.3. Formulate final text

Based on accepted theses — formulate the coherent text of the "Solution Concept" section. Preserve the agreed formulations as much as possible — correction is only allowed for coherence and conciseness.

**Formatting** (see standard):
- Group theses into semantic blocks, each block — a subsection (`###`)
- Prefer lists and structural elements over continuous text. Multiple ideas in a paragraph — break into points
- Connections between components (routes, conditions, dependencies) — as lists, not prose
- Start each semantic block with the key thesis in **bold**, followed by elaboration
- Technical identifiers — in `code` format

Agree on the text with the user. If edits are needed — use the dialog from 2.2 to agree on them.

#### 2.4. Write result

After final approval — write the text to the TI file.

### Stage 3. Filling Other Sections

> **🔴 CRITICAL:** After completing this stage — MUST re-read this guide before proceeding to the next stage.

**Important:** Execute each sub-stage below **only** if the corresponding section exists in the loaded standard. If a section is absent — skip the sub-stage entirely.

#### 3.1. Decision Matrix

Filled only for decisions that are important and merit documentation.

1. If there were dialogs about critical decisions in step 1.6 (security, load, infrastructure) — propose including them in the Decision Matrix
2. Analyze all accepted decisions from Stages 1–2 — identify which ones merit a Decision Matrix entry. Present the list to the user for them to indicate which decisions need entries
3. If nothing identified or the user declined all — section is not filled, proceed to 3.2
4. For each user-indicated decision — run independent analysis, propose a row formulation (what was decided / what was chosen / what alternatives were considered and why rejected / why this choice). Agree in free-form dialog
5. Form the final section text. Agree with the user:
   - **Agree**
   - **Skip, not important**
   - **Disagree, need to discuss**
6. After agreement — write to the TI file

#### 3.2. Security **[subagent]**

Filled **always** (when present in the standard).

**If risks were identified earlier (Stages 1–2):**
- Present each identified risk to the user:
  - **Agree** — risk is included
  - **Skip, not important** — risk is not included
  - **Disagree, need to discuss** — proceed to discussion

**If no risks were identified earlier:**
- Ask the user: do they see security risks in this task
- If the user sees no risks — proceed to formatting (see "No risks" below)
- If the user sees risks or there are doubts — launch research **[subagent]**. Present findings to the user:
  - **Agree** — risk is included
  - **Skip, not important** — risk is not included
  - **Disagree, need to discuss** — proceed to discussion

**No risks:** fill the section with a statement that the task carries no security risks, with justification.

**Risks agreed:**
1. Formulate all agreed risks, write to the file
2. For each risk — propose a mitigation (how we address it) or justification (why the risk is acceptable). Draft the initial formulation independently based on collected data
3. Ask the user about each solution separately. If the user disagrees — listen to their variant, critically analyze, resolve disagreements in dialog
4. After consensus on each — write to the file

#### 3.3. Performance **[subagent]**

Filled **always** (when present in the standard).

1. Launch performance and load risk analysis **[subagent]**
2. Present analysis results to the user. Agree on each risk:
   - **Agree** — risk is included
   - **Skip, not important** — risk is not included
   - **Disagree, need to discuss** — proceed to discussion
3. If no risks found or the user declined all — fill the section with a statement that the task carries no performance risks, with justification
4. For each agreed risk — propose a mitigation or justification. Draft the initial formulation independently based on collected data
5. Ask the user about each solution separately. If the user disagrees — listen to their variant, critically analyze, resolve disagreements in dialog
6. After consensus on each — write to the file

#### 3.4. Release & Rollback **[subagent]**

Filled **always** (when present in the standard).

1. Launch release specifics analysis **[subagent]**: feature flags, migrations, inter-service dependencies, deploy order. Determine a rollback plan
2. Present analysis results to the user. Agree on each point:
   - **Agree** — point is included
   - **Skip, not important** — point is not included
   - **Disagree, need to discuss** — proceed to discussion
3. If no specifics found or the user declined all — fill the section with a statement that the release is standard and rollback is trivial, with justification
4. For each agreed point — formulate description and resolution. Draft the initial formulation independently based on collected data
5. Ask the user about each point separately. If the user disagrees — listen to their variant, critically analyze, resolve disagreements in dialog
6. After consensus on each — write to the file

#### 3.5. Deepen **[subagent]**

> Executed **once only**. On re-entry to previous stages (after accepting improvements) — this stage is NOT executed again. After Deepen — proceed directly to Completion (4.1, 4.2).

##### 3.5.1. Deep analysis

Launch a deep investigation of the accepted solution **[subagent]**. Goal — identify potential flow improvements that may go beyond the original task scope.

Context for the analysis:
- Full text of the current TI document
- Codebase context collected in previous stages
- Original task requirements

Look for:
- Non-obvious optimizations of interactions between components
- Opportunities to simplify or unify flows
- Missed edge cases worth covering at the concept level
- Adjacent improvements that logically make sense to implement alongside the main task

##### 3.5.2. Filter proposals

Each found proposal — validate in a separate instance **[subagent]**. One instance per proposal. Exception: tightly related proposals that are part of a single flow may be analyzed together in one instance.

Validation answers the question: **is this improvement truly needed?**
- Does it add real value for the user or the system?
- Is the implementation complexity justified?
- Does it overload the solution with unnecessary functionality?

Proposals that do not pass filtering are discarded without presenting to the user.

##### 3.5.3. Approval

**If operating in an automated pipeline (no direct user interaction):** all proposals that passed filtering are accepted automatically.

**If operating in interactive mode:** present filtered proposals to the user. For each — briefly: what is proposed, why, what effect. The user decides which to accept.

##### 3.5.4. Apply

If there are accepted improvements — return to Stage 2 (Formulating the Solution Concept) and re-run stages 2–3 incorporating the new additions. Stage 3.5 on the second pass is **NOT executed** — after stage 3.4 proceed directly to Completion.

If no proposals or all declined — proceed to Completion (4.1).

### Completion

#### 4.1. Final review

Re-read the entire TI document. Ensure consistency and coherence. Inform the user that the TI is ready.

#### 4.2. Publishing

> **Skip this stage entirely if running as a subagent or part of an automated pipeline.**

Propose to the user to publish the document. Check available external tool integrations (task trackers like Jira/Linear/GitHub, knowledge bases like Confluence/Notion, etc.) and offer them as publishing targets. Ask where the user wants to publish. If the user provides a target — publish. If the user declines — done.

---

## Built-in Standard Reference

Used as fallback when no standard file exists at the configured `standard_path`. For the full standard, see the `standard.md` file distributed with this skill.

**Required sections:** Introduction, Solution Concept, Decision Matrix (when alternatives exist), Security, Performance, Release & Rollback.

**Key principles:**
- Describe the concept, not implementation details
- Only key fields that affect logic
- Every risk section filled always — "no risks" with justification if none
- No full schemas, no pseudocode, no business requirement duplication
- Diagrams replace text, not duplicate it
