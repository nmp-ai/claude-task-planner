# Jira (Atlassian MCP)

Tools come from the Atlassian connector. Tool names below are the base names; the full name carries the connector prefix (`mcp__<id>__<name>`). If a tool is not loaded, find it with ToolSearch (e.g. `+jira create`).

## Tools

| Purpose | Tool |
|---|---|
| Get `cloudId` / site | `getAccessibleAtlassianResources` |
| Read source ticket | `getJiraIssue` |
| Issue types of a project | `getJiraProjectIssueTypesMetadata` |
| Fields of an issue type (find story point field, required fields) | `getJiraIssueTypeMetaWithFields` — large response: call it **once, for the Story type only**; do not call it for Sub-task (hours use `timetracking`) |
| Duplicate search | `searchJiraIssuesUsingJql` |
| Create issue | `createJiraIssue` |
| Update issue (approved "update" actions only) | `editJiraIssue` |
| Link types / create link | `getIssueLinkTypes`, `createIssueLink` |
| Assignee lookup (only if the user asks) | `lookupJiraAccountId` |

If more than one Atlassian site is accessible, ask which one to use.

## Field mapping (`createJiraIssue`)

Top-level parameters: `cloudId`, `projectKey`, `issueTypeName`, `summary`, `description`, `contentFormat`, `parent`. Every other field goes inside **`additional_fields`**.

| Planner field | Parameter | Notes |
|---|---|---|
| Story | `issueTypeName: "Story"` | |
| Bug | `issueTypeName: "Bug"` | |
| Sub-task | `issueTypeName: "Sub-task"` / `"Subtask"` | Name varies per project; take it from issue type metadata (`subtask: true`) |
| Title | `summary` | Plain title only — no `[P]`, refs (`S1.2`), or priority markers |
| Description | `description` + `contentFormat: "markdown"` | Markdown from the template |
| Parent (Story → Epic, Sub-task → Story) | `parent: "ABC-123"` | Issue key as a string |
| Priority P1 / P2 / P3 | `additional_fields.priority = { "name": "High" \| "Medium" \| "Low" }` | Use the project's actual priority names if they differ |
| Story points | `additional_fields.customfield_xxxxx = 5` | Field is named `Story point estimate` (team-managed) or `Story Points` (company-managed); discover the id via `getJiraIssueTypeMetaWithFields`, never hard-code |
| Hours (Sub-task) | `additional_fields.timetracking = { "originalEstimate": "4h" }` | `"30m"` for 0.5h; requires time tracking on the project |
| Labels | `additional_fields.labels = ["ai-planned", …]` | |

Example (Sub-task):

```json
{
  "cloudId": "<cloudId>",
  "projectKey": "ABC",
  "issueTypeName": "Subtask",
  "parent": "ABC-101",
  "summary": "BE: Add POST /auth/password-reset/request endpoint",
  "contentFormat": "markdown",
  "description": "…",
  "additional_fields": {
    "labels": ["ai-planned"],
    "timetracking": { "originalEstimate": "4h" }
  }
}
```

If a field is not on the create screen, create the issue without it and report it in the final summary.

## Update mapping (`editJiraIssue`)

Parameters: `cloudId`, `issueIdOrKey`, `fields`, `contentFormat: "markdown"`. Unlike create, **all** fields go inside `fields` (no `additional_fields`): `summary`, `description`, `priority`, `labels`, `customfield_xxxxx` (story points), `timetracking`. Send only fields that change. Passing `null` clears a field — never do that unless it is in the approved diff. For `labels`, send the full list (existing labels + `ai-planned`).

## Links (`createIssueLink`)

Create links only after all issues exist.

| Planner relation | `type` | `inwardIssue` | `outwardIssue` |
|---|---|---|---|
| A `Blocked by` B | `Blocks` | **B** (the blocker) | **A** (the blocked issue) |
| Story ↔ source ticket | `Relates` | Story | source ticket |

Skip the `Relates` link when the source ticket is already the Story's parent. After the first `Blocks` link, read one issue back with `getJiraIssue` to confirm the direction before creating the rest.

## Duplicate search (JQL)

Run these in parallel. Combine all Story titles into **one** summary query with `OR`, not one query per Story.

```
project = <KEY> AND labels = ai-planned AND issue in linkedIssues(<SOURCE-KEY>)
project = <KEY> AND parent = <PARENT-KEY>
project = <KEY> AND statusCategory != Done AND (summary ~ "<words from S1>" OR summary ~ "<words from S2>" OR …)
```
