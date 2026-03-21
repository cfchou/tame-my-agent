---
name: plan-grilling
description: Relentless interview skill for any plan or design document — OpenSpec artifacts (`design.md`, `tasks.md`), RFCs, ADRs, PRDs, execution plans, or any Markdown design doc. Use this whenever the user asks to review, critique, pressure-test, or de-risk a design or plan, regardless of whether the document follows the OpenSpec format. Drive a branch-by-branch grilling, resolve decision and sequencing dependencies explicitly, and then update the document with all agreed clarifications.
license: MIT
---

# Plan Grilling

## Purpose

Use this skill to stress-test any plan or design document through an intensive interview.
The goal is shared understanding, not quick approval.

This skill should:

- interrogate every material section of the target document
- walk each branch of the design tree and expose hidden coupling
- resolve dependencies one-by-one (design or execution sequencing)
- capture clarified outcomes and update the target document

## When To Use

Use this skill whenever the user asks to:

- review or critique any design document — RFC, ADR, PRD, tech spec, OpenSpec `design.md`, or equivalent
- pressure-test any proposal before implementation
- analyze tradeoffs and edge cases in a design
- challenge assumptions, missing constraints, or unresolved design choices
- review or critique any execution plan — task list, checklist, OpenSpec `tasks.md`, or equivalent
- de-risk task order, dependencies, and acceptance criteria before implementation
- stress-test a plan or design doc that is not part of any OpenSpec workflow

Default to this skill for any design or plan review even if the user does not say "grilling" and even if the doc is not in OpenSpec format.

## Non-Goals

- do not jump into implementation details before core design/task sequencing decisions are settled
- do not accept vague answers when a decision boundary is still ambiguous
- do not end at feedback only; finalize by updating the target document

## Core Principles

Apply these throughout the interview and while editing the target document:

- Think deeply and critically to surface edge cases and hidden complexities.
- Break the system into small units with:
  - one clear purpose
  - well-defined interfaces
  - independent testability
- Prioritize:
  - Simplicity
  - Testability
  - Explicitness
- Apply:
  - YAGNI
  - DRY
  - SOLID

## Interview Posture

Be relentless, but collaborative.

- ask specific, decision-forcing questions, **one question at a time**.
- challenge assumptions and implicit defaults
- request concrete invariants, not intentions
- keep a running map of unresolved decisions and blockers
- drive toward closure for each open branch before moving on

If there is uncertainty, state the fork and force a decision path.

## Workflow

Follow this sequence in order.

1. Resolve the target document (OpenSpec change or plain doc) and detect its format.
2. Check current change status and workflow schema (OpenSpec path only).
3. Detect mode (design / tasks / combined) and map the document into a decision tree.
4. Run branch-by-branch grilling until each branch is resolved.
5. Resolve cross-branch dependencies one-by-one.
6. Confirm shared understanding with explicit decision records.
7. Present a summary of all agreed changes, get user confirmation, then update the target document.

## Step 0: Resolve The Target Document

Identify the document to grill using one of two paths.

### Path A — OpenSpec Change

Use this path when:
- Already in an OpenSpec change context, OR
- the user specifies an OpenSpec change, OR
- no file path is given and the current project has OpenSpec changes

1. If a change name is provided and unambiguous, use it.
2. If no change name is provided, infer only when clearly supported by conversation context.
3. If context is vague or ambiguous, run:

```bash
openspec list --json
```

Sort by most recently modified and present the top 2-3 changes. Use the AskUserQuestion tool to let the user choose. Do not auto-select.

For each option, show:
- change name
- schema (`schema` field, or `spec-driven` if missing)
- status (for example: `0/5 tasks`, `complete`, `no tasks`)
- recency from `lastModified`

Mark the most recently modified change as `(Recommended)`.

IMPORTANT: Never guess or pick a change automatically when user intent is unclear.

After selecting a change, run:

```bash
openspec status --change "<name>" --json
```

Parse and carry forward:
- `schemaName`
- `artifacts` and each artifact status (`done`, `ready`, `blocked`)
- `isComplete`

Use this state to shape interview depth and focus:
- If complete, confirm whether user wants post-completion design audit or forward changes.
- If blocked artifacts exist, prioritize grilling around blocker roots.
- If artifacts are ready/in-progress, focus on near-term decision risks and acceptance gaps.

### Path B — Plain Document

Use this path when:
- the user provides a file path to a `.md` file, OR
- the user pastes document content directly in chat, OR
- the `openspec` CLI is unavailable or the document is not part of an OpenSpec change

Steps:
1. If a file path is given, read the file directly.
2. If content is pasted, use it as-is.
3. Skip all `openspec` CLI calls.
4. Record the file path (or note "pasted content") to use when updating the document in Step 5.

If neither a file path nor content is available, ask the user to provide the document before proceeding.

## Mode Selection

Detect mode by reading the document content. Look for these signals:

| Signal                                                                                                                                       | Mode         |
|----------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| Headings or sections for: Architecture, Design, Schema, Data Model, Interfaces, Non-Goals, Constraints, System Overview, Decisions           | **Design**   |
| Headings or sections for: Tasks, Steps, Checklist, Sprint Plan, Execution Plan, Acceptance Criteria, Definition of Done, numbered task lists | **Tasks**    |
| Both sets of signals present                                                                                                                 | **Combined** |

For OpenSpec documents, also apply explicit rules:
- Target is `design.md` → Design mode
- Target is `tasks.md` → Tasks mode

If mode is still ambiguous after reading the document, ask the user once:
*"Is this primarily a design doc, an execution plan, or does it contain both?"*

For Combined mode: run the Design grilling pass first, then the Tasks pass. Cross-check consistency between the two passes before finalizing.

## Step 1: Build The Decision Tree

Start by extracting a structure from the target document.

For design documents, see [references/grilling-design.md](references/grilling-design.md)

For execution plans, see [references/grilling-tasks.md](references/grilling-tasks.md)


## Step 2: Grilling Loop (Per Branch)

For each branch, repeatedly ask until there is a clear, testable decision.

Question categories:

- Intent: what exact problem is this branch solving?
- Alternatives: what options were considered and rejected?
- Boundaries: what is explicitly in/out of scope?
- Contracts: what interfaces exist and who owns them?
- Invariants: what must always be true?
- Failure: how does this fail, and how is failure detected?
- Operations: what observability and rollback paths exist?
- Evolution: what is the next likely change and cost?

Do not move forward while answers are still non-falsifiable.

## Step 3: Dependency Resolution

After each branch pass, resolve dependencies explicitly.

For each dependency:

- identify prerequisite decision
- identify downstream decisions blocked by it
- decide in strict order (prerequisite first)
- verify no contradictions against prior branches

If a dependency remains unresolved, mark it as blocker and continue only where safe.

## Step 4: Decision Ledger

Maintain a compact ledger during the interview.

Each decision entry should include:

- Decision
- Rationale
- Alternatives considered
- Tradeoffs accepted
- Affected sections in the target document
- Follow-up needed (if any)

Use this ledger to ensure final edits are complete and internally consistent.

## Step 5: Update Target Document (Required)

At the end of the grilling phase, present a summary of all agreed clarifications and proposed edits before touching the file.

### For OpenSpec documents (Path A)

Ask permission, then update the target file directly:
- `design.md` in design mode
- `tasks.md` in tasks mode

### For plain documents (Path B)

1. Present a structured summary in chat showing:
   - every agreed decision (with rationale)
   - every section to be added, removed, or reworded
   - any open questions to be recorded
2. Ask the user to confirm: *"Shall I apply these changes to `<filename>`?"*
3. Only after confirmation, edit the file in place.
4. If the document was pasted content (no file path), output the full updated document in chat instead.

### Editing requirements (both paths)

- fold in all clarified decisions and constraints
- remove ambiguity and hand-wavy language
- align terminology across sections
- add explicit interfaces, invariants, and failure handling
- add explicit task dependencies, owners, and acceptance criteria when in tasks mode
- preserve scope discipline (YAGNI) and remove speculative design
- deduplicate repeated rules (DRY)
- keep responsibilities and abstractions aligned (SOLID)
- for non-OpenSpec docs: normalize section structure to (problem → boundaries → decisions → open questions) only if the user agrees during the confirmation step

If any open questions remain, list them in a dedicated "Open Questions" section with owner and unblock condition.

## Quality Bar Before Finishing

Before ending, verify:

- every critical branch has a decided or explicitly deferred outcome
- each deferred outcome has an owner and trigger condition
- cross-branch dependencies are consistent
- decisions are specific enough to implement and test independently
- target file reflects the shared understanding from the interview

## Output Style

When reporting back in chat:

1. Show the resolved decisions and remaining blockers.
2. Summarize key tradeoffs and why they were chosen.
3. Confirm that the target document was updated and which sections changed.
4. Follow the style of the target file such as diagrams, code snippets, tables.

Keep language crisp, concrete, and evidence-driven.

## Example Triggers

- "Review this OpenSpec change design and challenge it hard."
- "Grill my `design.md` before we implement."
- "Pressure-test this OpenSpec proposal and find hidden coupling."
- "Walk every decision branch and update `design.md` with what we settle."
- "Grill my `tasks.md` and fix dependency order before coding."
- "Challenge this OpenSpec task plan and update `tasks.md` with clarified acceptance criteria."
- "Review this RFC and pressure-test every assumption."
- "Grill this PRD before we start building."
- "Challenge my ADR and help me close all open decisions."
- "Here's my sprint plan — grill it and find sequencing gaps."
- "Stress-test this tech spec and update it with whatever we decide."
