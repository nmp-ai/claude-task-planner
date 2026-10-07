---
description: Create the approved breakdown as issues in Jira or Linear
argument-hint: <jira|linear> <PROJECT_KEY | TEAM> [parent issue key]
---

Create the most recent approved breakdown from this conversation in the target tool.

Arguments: $ARGUMENTS

- 1st: provider — `jira` or `linear` (required).
- 2nd: Jira project key or Linear team key (required). If missing, ask; never pick one yourself.
- 3rd (optional): parent issue key to attach Stories to (e.g. an Epic). If omitted and the source ticket is an Epic, use the source ticket.

Read the field mapping first: `providers/jira.md` or `providers/linear.md`.

## Step 1 — Preflight (read-only)

1. Confirm the MCP for the provider is connected. If not, stop and tell the user to authorize it (`/mcp` in a `claude` terminal or claude.ai connector settings).
2. Resolve the project/team and its issue types, priorities, and the estimate fields as described in the provider file.
3. If `/validate` was not run or reported ERRORs, stop and say so.

## Step 2 — Duplicate check (read-only)

Search the target for issues that may already exist (queries in the provider file):
- issues linked to the source ticket with label `ai-planned`;
- children of the parent;
- summaries that closely match each Story title.

For each match, propose an action: **skip**, **update** (show field diff), or **create anyway**.

## Step 3 — Final preview and approval

Show exactly what will happen:

| # | Action | Type | Title | Parent | SP | Hours | Priority | Labels | Links |
|---|---|---|---|---|---|---|---|---|---|

Plus the full description of the first Story as a sample. Then ask:

> Create these N issues in `<PROJECT>`? (yes / edit / cancel)

**Do not proceed without an explicit "yes".** Any edit → show the preview again.

## Step 4 — Create

Order: Stories → Sub-tasks (with parent) → issue links (`Blocked by`, link to source ticket).

- Add label `ai-planned` to every issue.
- Description ends with `Covers: FR-…` and `Source: <source key/URL>`.
- On an error, stop, report what was created so far and what failed, and ask how to continue. Do not retry blindly and do not delete anything.

## Step 5 — Report

| Ref | Key | Title | URL |
|---|---|---|---|

Plus any fields that could not be set (e.g. no story point field on the screen) and suggested fixes.
