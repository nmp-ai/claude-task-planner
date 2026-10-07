---
description: Resolve open questions in the current spec, in rounds of up to 5 questions, until none are left
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
   - Unanswered items stay as open questions. Convert one to an explicit assumption only when the user says so.
   - The spec in effect is the `/analyze` output plus these changes. Output the full spec only if the user asks.
5. **Repeat** steps 1–4 (each round ≤ 5 questions, including new gaps raised by the answers) until no `[NEEDS CLARIFICATION]` is left. Show `Open questions remaining: N` at the end of each round.
6. Stop early only when the user says so (e.g. "enough", "đủ rồi"). Then list every remaining item and ask whether to convert each to an explicit assumption or keep it open. Any item kept open will block `/create-tasks` (see `/validate`).
7. When nothing is open, suggest `/breakdown` as the next step.

Do not create or edit anything in Jira/Linear.
