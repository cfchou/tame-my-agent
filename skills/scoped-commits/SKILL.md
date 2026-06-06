---
name: scoped-commits
description: Use when writing, reviewing, or rewriting Git commit messages in Scoped Commits format, especially when the user asks for commit messages, commit style, scopes, or alternatives to Conventional Commits.
---

# Scoped Commits

Use this skill to produce commit messages that foreground the part of the codebase being changed.

Source: https://scopedcommits.com/

## Format

Normal commits should use this structure:

```text
<scope>: <description>

[optional body]

[optional trailer(s)]
```

- `<scope>` is the subsystem, area, module, package, command, feature, or concern touched by the commit.
- `<description>` is a short summary of what changed.
- The body explains details, rationale, caveats, or behavior changes when the subject alone is not enough.
- Trailers carry metadata such as issue links, ticket IDs, sign-offs, or co-authors.

## When To Use

Use Scoped Commits when the user asks to:

- write a commit message
- rewrite or improve a commit message
- review commit message quality
- choose a commit scope
- define commit message guidelines
- avoid or replace Conventional Commits
- create commits for a project that prefers readable, contributor-focused logs

## Core Rules

- Put the scope first because contributors, debuggers, and incident responders scan logs by affected area.
- Use the most specific meaningful scope that helps someone find related changes later.
- Prefer project vocabulary over generic labels.
- Keep the description concise and behavior-focused.
- Do not force `feat`, `fix`, `chore`, or other Conventional Commits type prefixes unless the project explicitly requires them.
- Do not generate changelogs from Scoped Commits alone; commit logs and changelogs serve different audiences.

## Scope Selection

Choose scopes in this order:

1. A concrete subsystem, package, feature, directory, command, integration, or module.
2. A broader domain that covers the touched areas when one narrow module is insufficient.
3. Multiple scopes separated by commas when the change genuinely spans peers.
4. `treewide`, `all`, or `global` when the change intentionally touches the whole tree.

Good scopes are usually nouns or paths that a maintainer would search for in `git log`.

Examples:

```text
auth: reject expired refresh tokens
net/http: preserve cookie jar docs links
gitlab-ci: update macOS image
xwayland: 24.1.11 -> 24.1.12
treewide: normalize generated snapshot names
```

## Descriptions

Write descriptions as short imperative or descriptive summaries, matching the repository's existing style.

Prefer:

```text
auth: reject expired refresh tokens
```

Avoid vague descriptions:

```text
auth: fixes
auth: update stuff
misc: changes
```

Avoid Conventional Commits style unless required:

```text
fix(auth): reject expired refresh tokens
```

## Bodies

Add a body when the commit needs context that does not fit in the subject.

Use the body to explain:

- why the change was needed
- what behavior changed
- important implementation tradeoffs
- compatibility or migration notes
- testing or verification details when useful

Do not repeat the subject in the body.

## Trailers And Tickets

Ticket IDs can go after the scope, in the body, or in trailers. Prefer the repository's existing convention when visible.

Examples:

```text
auth (TICKET-123): reject expired refresh tokens
```

```text
auth: reject expired refresh tokens

Expired refresh tokens now fail before session renewal.

Refs: TICKET-123
```

## Special Commits

Reverts, merges, and other special commits may use Git defaults or the repository's established special-commit format.

Do not over-normalize these if the default message is clearer:

```text
Revert "auth: reject expired refresh tokens"
```

## Workflow

When generating a commit message:

1. Inspect the change or user-provided summary.
2. Infer the smallest useful scope from affected files, modules, or behavior.
3. Check recent commit style if the repository is available.
4. Produce one recommended message first.
5. Include alternatives only when the scope or wording is genuinely ambiguous.

When reviewing a commit message:

1. Verify it follows `<scope>: <description>` for normal commits.
2. Check that the scope is useful to someone scanning history.
3. Check that the description says what changed, not merely that something changed.
4. Preserve valid special commit formats.
5. Suggest a corrected message when there is a problem.

## Response Style

- If the user wants a commit message, output the message directly in a fenced `text` block unless they ask for explanation.
- If the user asks for multiple options, keep options short and rank the best one first.
- If there is not enough context to choose a scope, ask for the changed files or inspect the diff when available.
