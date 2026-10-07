---
description: Resolve open questions in the current spec with up to 5 targeted questions
argument-hint: [answers or extra context]
---

Work on the most recent spec produced by `/analyze` in this conversation. If none exists, ask the user to run `/analyze` first.

Extra input: $ARGUMENTS

## Steps

1. Collect all `[NEEDS CLARIFICATION]` items plus any gaps that would materially change scope, estimates, or acceptance criteria.
2. Rank by impact (scope > data/security > UX > edge cases). Keep **at most 5** questions.
3. Ask them in one message. For each question:
   - Reference the affected FR.
   - Offer 2–4 concrete options and mark a recommended one, so the user can answer briefly (e.g. "1B, 2A").
4. When the user answers, output the **updated spec in full** with:
   - Answers folded into requirements (no `[NEEDS CLARIFICATION]` left for answered items).
   - A `### Clarifications` section: `Q → A` per item.
   - Unanswered items kept as open questions or converted to explicit assumptions (say which).
5. Suggest `/breakdown` as the next step.

Do not create or edit anything in Jira/Linear.
