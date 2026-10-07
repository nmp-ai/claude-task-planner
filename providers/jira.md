# Jira (Atlassian MCP)

Tools come from the Atlassian connector. Tool names below are the base names; the full name carries the connector prefix (`mcp__<id>__<name>`). If a tool is not loaded, find it with ToolSearch (e.g. `+jira create`).

## Tools

| Purpose | Tool |
|---|---|
| Get `cloudId` / site | `getAccessibleAtlassianResources` |
| Read source ticket | `getJiraIssue` |
| Issue types of a project | `getJiraProjectIssueTypesMetadata` |
| Fields of an issue type (find story point field, required fields) | `getJiraIssueTypeMetaWithFields` |
| Duplicate search | `searchJiraIssuesUsingJql` |
| Create issue | `createJiraIssue` |
| Update issue (approved "update" actions only) | `editJiraIssue` |
| Link types / create link | `getIssueLinkTypes`, `createIssueLink` |
| Assignee lookup (only if the user asks) | `lookupJiraAccountId` |

If more than one Atlassian site is accessible, ask which one to use.

## Field mapping

| Planner field | Jira field | Notes |
|---|---|---|
| Story | issue type `Story` | |
| Bug | issue type `Bug` | |
| Sub-task | issue type `Sub-task` / `Subtask` | Name varies per project; take it from issue type metadata (`subtask: true`) |
| Title | `summary` | |
| Description | `description` | Markdown from the template |
| Parent (Story → Epic, Sub-task → Story) | `parent: { key }` | |
| Priority P1 / P2 / P3 | `priority.name` = `High` / `Medium` / `Low` | Use the project's actual priority names if they differ |
| Story points | custom field named `Story point estimate` (team-managed) or `Story Points` (company-managed) | Discover the `customfield_xxxxx` id via field metadata; never hard-code |
| Hours (Sub-task) | `timetracking.originalEstimate` e.g. `"4h"`, `"30m"` | Requires time tracking enabled on the project |
| Labels | `labels: ["ai-planned", …]` | |
| Blocked by | link type `Blocks` (inward: "is blocked by") | Create after all issues exist |
| Source ticket | link type `Relates` | Skip if the source is already the parent |

If a field is not on the create screen, create the issue without it and report it in the final summary.

## Duplicate search (JQL)

```
project = <KEY> AND labels = ai-planned AND issue in linkedIssues(<SOURCE-KEY>)
project = <KEY> AND parent = <PARENT-KEY>
project = <KEY> AND summary ~ "<distinctive words from title>" AND statusCategory != Done
```
