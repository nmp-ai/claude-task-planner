---
description: Break the current spec into Stories and Sub-tasks with priorities and estimates
argument-hint: [extra constraints, e.g. "FE only" or "max 3 stories"]
---

Break down the most recent spec in this conversation (from `/analyze` or `/clarify`). If none exists, ask the user to run `/analyze` first.

Extra constraints: $ARGUMENTS

Follow `.claude/skills/task-writing/SKILL.md` and the templates in `templates/`. Look at `examples/` for the expected level of detail.

## Rules

- Hierarchy: **Story → Sub-task**. For a bug, use one Bug (template `templates/bug.md`) with Sub-tasks only if the fix spans several areas.
- Slice Stories **vertically** (each delivers user-visible, testable value). Avoid "FE story" / "BE story" splits; use Sub-tasks for that.
- Priority P1/P2/P3. P1 Stories together form the MVP.
- Every Story: `Covers: FR-…`, acceptance criteria in Given/When/Then, story points (1/2/3/5/8/13).
- Every Sub-task: one owner-sized unit of work, hours (0.5–16h), `[P]` if parallelizable, `Blocked by:` when ordered.
- Include Sub-tasks for tests and, when relevant, migration, feature flag, docs, and release steps.
- Remaining `[NEEDS CLARIFICATION]` items: keep them visible in the affected Story's "Notes" and flag them in the summary.

## Output

1. **Overview table**

| Ref | Type | Title | Priority | SP | Hours | Covers | Blocked by |
|---|---|---|---|---|---|---|---|
| S1 | Story | … | P1 | 5 | 14 | FR-001, FR-002 | – |
| S1.1 | Sub-task | [P] … | | | 4 | | – |

2. **Details** for each Story and its Sub-tasks, using the templates.
3. **Totals**: SP and hours per priority and overall.
4. **Coverage**: list each FR/NFR → Story refs. Any uncovered item is flagged.

End by suggesting `/validate`, then approval and `/create-tasks <jira|linear> <PROJECT_KEY|TEAM>`.

Do not create or edit anything in Jira/Linear.
