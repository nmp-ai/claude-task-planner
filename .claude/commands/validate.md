---
description: Check the current spec and breakdown for gaps, contradictions, and estimate issues
---

Validate the most recent spec and breakdown in this conversation. If the breakdown is missing, ask the user to run `/breakdown` first.

Run every check below and report findings. Do not silently fix anything.

## Checks

**Coverage**
- Every FR/NFR is covered by at least one Story, or explicitly out of scope.
- Every Story covers at least one FR (no orphan Stories).

**Consistency**
- No contradictions between requirements, AC, assumptions, and clarifications.
- Terminology is consistent (same entity, same name).
- `Blocked by` references exist and form no cycles.

**Quality** (Definition of Ready from `.claude/skills/task-writing/SKILL.md`)
- Titles follow the format; AC are Given/When/Then and measurable; no vague words.
- No unresolved `[NEEDS CLARIFICATION]` on P1 Stories.

**Estimates**
- Story SP in {1,2,3,5,8,13}; Sub-task hours in 0.5–16.
- Flag a Story whose total hours look inconsistent with its SP compared to the other Stories in this breakdown.
- Flag Stories > 8 SP as candidates to split.

## Output

```
## Validation — <PASS | PASS WITH WARNINGS | FAIL>

| Severity | Check | Item | Finding | Suggested fix |
|---|---|---|---|---|
| ERROR | Coverage | FR-004 | Not covered by any Story | Add to S2 or mark out of scope |
| WARN  | Estimate | S3 | 13 SP | Split by … |
```

- **ERROR** blocks creation; **WARN** does not.
- If there are findings, offer to apply the suggested fixes and show the revised breakdown.
- If PASS (or only WARN), ask the user to approve and run `/create-tasks <jira|linear> <PROJECT_KEY|TEAM>`.
