---
name: openspec-grilling
description: Relentless interview skill for `design.md` and `tasks.md`. Use this whenever the user asks to review, critique, pressure-test, or de-risk an OpenSpec design or execution plan. Drive a branch-by-branch grilling, resolve decision and sequencing dependencies explicitly, and then update the relevant OpenSpec file with all agreed clarifications.
license: MIT
---

# OpenSpec Grilling

## Purpose

Use this skill to stress-test an OpenSpec change through an intensive interview.
The goal is shared understanding, not quick approval.

This skill should:

- interrogate every material section of the target OpenSpec file (`design.md` or `tasks.md`)
- walk each branch of the design tree and expose hidden coupling
- resolve dependencies one-by-one (design or execution sequencing)
- capture clarified outcomes and update the target file

## When To Use

Use this skill whenever the user asks:

- review or critique an OpenSpec design
- pressure-test an OpenSpec proposal before implementation
- analyze tradeoffs and edge cases in `design.md`
- challenge assumptions, missing constraints, or unresolved design choices
- review or critique an OpenSpec `tasks.md` execution plan
- de-risk task order, dependencies, and acceptance criteria before implementation

Default to this skill for OpenSpec design or task-plan reviews even if the user does not say "grilling."

## Non-Goals

- do not jump into implementation details before core design/task sequencing decisions are settled
- do not accept vague answers when a decision boundary is still ambiguous
- do not end at feedback only; finalize by updating the target OpenSpec file

## Core Principles

Apply these throughout the interview and while editing `design.md`:

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

1. Verify change intent and select the target OpenSpec change.
2. Check current change status and workflow schema.
3. Detect target file (`design.md` or `tasks.md`) and map it into a decision tree.
4. Run branch-by-branch grilling until each branch is resolved.
5. Resolve cross-branch dependencies one-by-one.
6. Confirm shared understanding with explicit decision records.
7. Update the target file to reflect all agreed changes and clarifications.

## Step 0: Verify Intent And Select Change

Input can optionally include a change name.

- If a change name is provided and unambiguous, use it.
- If no change name is provided, infer only when clearly supported by conversation context.
- If context is vague or ambiguous, you MUST prompt for available changes.

When prompting for selection:

1. Run:

```bash
openspec list --json
```

2. Sort by most recently modified and present the top 3-4 changes.
3. Use the AskUserQuestion tool to let the user choose. Do not auto-select.
4. For each option, show:
   - change name
   - schema (`schema` field, or `spec-driven` if missing)
   - status (for example: `0/5 tasks`, `complete`, `no tasks`)
   - recency from `lastModified`
5. Mark the most recently modified change as `(Recommended)`.

IMPORTANT: Never guess or pick a change automatically when user intent is unclear.

## Step 0.5: Check Current Status

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

## Mode Selection

Choose mode from user intent and available files:

- Design mode: target `design.md`.
- Tasks mode: target `tasks.md`.

If both are in scope, start with `design.md`, then verify `tasks.md` still aligns.

## Step 1: Build The Decision Tree

Start by extracting a structure from the target file.

For `design.md`, see [references/grilling-design.md](references/grilling-design.md) 

For `tasks.md`, see [references/grilling-tasks.md](references/grilling-tasks.md) 


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
- Affected sections in the target OpenSpec file
- Follow-up needed (if any)

Use this ledger to ensure final edits are complete and internally consistent.

## Step 5: Update Target OpenSpec File (Required)

At the end of the grilling phase, ask permission to update the target file:

- `design.md` in design mode
- `tasks.md` in tasks mode

Editing requirements:

- fold in all clarified decisions and constraints
- remove ambiguity and hand-wavy language
- align terminology across sections
- add explicit interfaces, invariants, and failure handling
- add explicit task dependencies, owners, and acceptance criteria when in tasks mode
- preserve scope discipline (YAGNI) and remove speculative design
- deduplicate repeated rules (DRY)
- keep responsibilities and abstractions aligned (SOLID)

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
3. Confirm that the target OpenSpec file was updated and which sections changed.
4. Follow the style of the target file such as diagrams, code snippets, tables.

Keep language crisp, concrete, and evidence-driven.

## Example Triggers

- "Review this OpenSpec change design and challenge it hard."
- "Grill my `design.md` before we implement."
- "Pressure-test this OpenSpec proposal and find hidden coupling."
- "Walk every decision branch and update `design.md` with what we settle."
- "Grill my `tasks.md` and fix dependency order before coding."
- "Challenge this OpenSpec task plan and update `tasks.md` with clarified acceptance criteria."
