# Claude Task Planner

A Claude Code workspace that turns requirements from Jira, Linear, or plain text into a spec and well-written, estimated tasks — and creates them in your task tool after you approve.

Inspired by Spec-Driven Development (GitHub Spec Kit, Kiro): EARS requirements, `[NEEDS CLARIFICATION]` markers, requirement-to-task traceability, and a validation gate. Everything stays in the chat until you approve; nothing is written to local files.

## Setup

1. Open this folder in Claude Code.
2. Connect a task tool:
   - **Jira**: enable the Atlassian connector in claude.ai connector settings (or add the Atlassian MCP server via `/mcp`).
   - **Linear**: authorize the Linear MCP server via `/mcp` in a `claude` terminal.

## Usage

```
/analyze ABC-123                 # or a URL, or pasted text
/clarify                         # optional: answer up to 5 questions
/breakdown                       # Stories → Sub-tasks, SP + hours, P1/P2/P3
/validate                        # coverage, contradictions, estimates
/create-tasks jira ABC           # preview → you say "yes" → issues created
/create-tasks linear ENG         # same for Linear
/create-tasks jira ABC ABC-100   # attach Stories to parent ABC-100
```

## Conventions

- Task content in English.
- Story → Sub-task. Stories: story points (1–13). Sub-tasks: hours (0.5–16).
- Every created issue gets the label `ai-planned` and a `Covers: FR-…` / `Source:` footer.
- Duplicate check against the target tool before anything is created.

## Layout

| Path | Purpose |
|---|---|
| `CLAUDE.md` | Agent role, constitution, workflow, conventions |
| `.claude/commands/` | `/analyze`, `/clarify`, `/breakdown`, `/validate`, `/create-tasks` |
| `.claude/skills/task-writing/` | Writing standard, Definition of Ready/Done |
| `templates/` | Story, Bug, Sub-task templates |
| `providers/` | Field mapping and MCP tools for Jira and Linear |
| `examples/` | Input → output examples |

## Customizing

- Team estimate scale, priority names, or labels → `CLAUDE.md` and `providers/*.md`.
- Title/AC style → `.claude/skills/task-writing/SKILL.md`.
- Add another tool (e.g. Asana, GitHub Issues) → new `providers/<tool>.md` and accept it in `create-tasks.md`.
