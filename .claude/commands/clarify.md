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
4. When the user answers, output **only the changes** to the spec (do not repeat unchanged sections):
   - Each changed or new FR/NFR/assumption in full, with its ID (answers folded in, no `[NEEDS CLARIFICATION]` left for answered items). Mark removed items as `FR-00x: removed — <reason>`.
   - A `### Clarifications` section: `Q → A` per item.
   - Unanswered items kept as open questions or converted to explicit assumptions (say which).
   - The spec in effect is the `/analyze` output plus these changes. Output the full spec only if the user asks.
5. Suggest `/breakdown` as the next step.

Do not create or edit anything in Jira/Linear.
