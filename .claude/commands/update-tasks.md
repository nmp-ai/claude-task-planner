---
description: Update existing Jira or Linear issues to match the current breakdown
argument-hint: <jira|linear> <PROJECT_KEY | TEAM> [ISSUE_KEY ...]
---

Update existing issues so they match the most recent validated breakdown in this conversation.

Arguments: $ARGUMENTS

- 1st: provider — `jira` or `linear` (required).
- 2nd: Jira project key or Linear team key (required). If missing, ask; never pick one yourself.
- Rest (optional): issue keys to update. If omitted, find the issues created for this source with the duplicate-search queries in the provider file, then ask the user to confirm the list before going further.

Read the field mapping first: `providers/jira.md` or `providers/linear.md`.

## Step 1 — Preflight (read-only)

1. Confirm the MCP for the provider is connected. If not, stop and tell the user to authorize it (`/mcp` in a `claude` terminal or claude.ai connector settings).
2. If there is no breakdown in this conversation, ask the user to run `/analyze` → `/breakdown` first. If `/validate` was not run on the current breakdown or reported ERRORs, stop and say so.

## Step 2 — Read current state (read-only)

Read all target issues in parallel (one message): summary, description, priority, labels, parent, estimate fields, status, and links.

## Step 3 — Match issues to the breakdown

| Ref | Key | Match basis |
|---|---|---|
| S1 | ABC-101 | Title + `Covers:` footer |

- Match by title and the `Covers:` / `Source:` footer. If a match is uncertain, ask the user.
- Breakdown item with no issue → propose **create** (with parent).
- Issue with no breakdown item → propose **leave as is**. Never delete or close issues.

## Step 4 — Diff and approval

Show only fields that change:

| # | Action | Key | Field | Current | New |
|---|---|---|---|---|---|
| 1 | update | ABC-101 | Priority | Medium | High |
| 2 | update | ABC-102 | Story points | 3 | 5 |
| 3 | create | — | Sub-task | — | BE: Add rate limit to reset endpoint |

- For descriptions, show a short summary of changed sections, not the full text. If the current description contains content the planner did not write (e.g. edited by a person), warn and propose merging instead of overwriting.
- Do not touch status, assignee, sprint, comments, or fields not in the mapping unless the user asks.
- Keep existing labels; only add `ai-planned` if missing.

Then ask:

> Apply these N changes in `<PROJECT>`? (yes / edit / cancel)

**Do not proceed without an explicit "yes".** Any edit → show the diff again.

## Step 5 — Apply

Run in parallel batches, waiting for each batch to finish: all updates → all creates → all new links. Never remove existing links.

On an error, stop, report what was applied so far and what failed, and ask how to continue. Do not retry blindly.

## Step 6 — Report

| Ref | Key | Action | Result | URL |
|---|---|---|---|---|

Plus any fields that could not be set and suggested fixes.
