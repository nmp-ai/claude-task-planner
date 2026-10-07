---
name: task-writing
description: Standard for writing Stories, Bugs, and Sub-tasks — titles, descriptions, acceptance criteria, Definition of Ready/Done, labels, and estimates. Use when analyzing requirements, breaking them down, validating, or creating tasks in Jira/Linear.
---

# Task Writing Standard

All task content is written in **English**.

## Titles

- Story: `[Area] <Verb> <object> <qualifier>` — e.g. `[Checkout] Allow guest users to pay with saved card`
- Bug: `[Area] <What is wrong> when <condition>` — e.g. `[Auth] Login fails with 500 when email contains "+"`
- Sub-task: `<Layer>: <Verb> <object>` — e.g. `BE: Add POST /orders/guest endpoint`
  - Layers: `FE`, `BE`, `Mobile`, `DB`, `API`, `QA`, `Infra`, `Docs`, `Design`, `Spike`
- ≤ 80 characters, imperative mood, no trailing period, no ticket keys in the title.
- Planning markers (`[P]`, refs like `S1.2`, priority) never go in the title; they live in their own columns/fields.

## Description

Use the matching template in `templates/`. Keep it scannable: short sections, bullets, no walls of text.

## Acceptance criteria

- Given/When/Then, one behavior per criterion, numbered `AC1`, `AC2`, …
- Measurable: replace vague words with values.

| Avoid | Write instead |
|---|---|
| fast | p95 response < 300 ms |
| user-friendly | completes in ≤ 3 clicks; labels on all inputs |
| handles errors | shows message "<text>" and keeps form input |
| etc., and so on | the explicit list |

- Cover: happy path, validation/error paths, permissions, empty states, and any NFR the Story covers.

## Estimates

- Story: story points, Fibonacci `1, 2, 3, 5, 8, 13`.
  - 1 = trivial, well known · 3 = typical · 8 = large, some unknowns · 13 = split it.
- Sub-task: hours `0.5–16`. Include testing and review time in the Sub-task that does the work, or a separate `QA:` Sub-task.
- Story hours = sum of its Sub-tasks (shown in previews, not set on the Story).
- If unknowns dominate, add a time-boxed `Spike:` Sub-task instead of padding.

## Labels

- Always: `ai-planned`.
- Optional by area: `frontend`, `backend`, `database`, `infra`, `qa` — only if the project already uses them.

## Definition of Ready (checked by `/validate`)

- [ ] Title follows the format
- [ ] Description uses the template; context explains *why*
- [ ] AC in Given/When/Then, measurable, include error paths
- [ ] `Covers:` lists at least one FR/NFR
- [ ] Estimate set (SP for Story, hours for Sub-task)
- [ ] Dependencies (`Blocked by`) identified
- [ ] No unresolved `[NEEDS CLARIFICATION]` on P1 items

## Definition of Done (included in every Story)

- Code reviewed and merged
- All AC verified (automated tests where practical)
- No new lint/type errors; CI green
- Docs/release notes updated if behavior changed
- Deployed to staging and smoke-tested
