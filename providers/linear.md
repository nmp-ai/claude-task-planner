# Linear (Linear MCP)

Tools come from the Linear MCP server (requires authorization: `/mcp` in a `claude` terminal). Tool names differ between server versions; find them with ToolSearch (e.g. `+linear issue`) before use.

## Tools (typical)

| Purpose | Tool (typical name) |
|---|---|
| Read source issue | `get_issue` |
| Teams / labels / states | `list_teams`, `list_issue_labels`, `list_issue_statuses` |
| Duplicate search | `list_issues` (filter by team, label, parent, query) |
| Create / update issue | `create_issue` / `update_issue` (or `save_issue` in newer versions) |
| Comment on source (only if the user asks) | `create_comment` |

## Field mapping

| Planner field | Linear field | Notes |
|---|---|---|
| Story | issue | Linear has no issue types; add label `story` if the team uses it |
| Bug | issue + label `bug` | |
| Sub-task | sub-issue (`parentId` = Story id) | |
| Title | `title` | |
| Description | `description` | Markdown from the template |
| Priority P1 / P2 / P3 | `priority` = 2 (High) / 3 (Medium) / 4 (Low) | 1 = Urgent, reserved for production incidents |
| Story points | `estimate` | Must match the team's estimate scale; if it is not Fibonacci, map to the nearest allowed value and report it |
| Hours (Sub-task) | — | No native field: add `**Estimate:** 4h` at the top of the description |
| Labels | `labelIds` incl. `ai-planned` | Create the label only after asking the user |
| Blocked by | relation `blocks` | If the MCP has no relation tool, write `Blocked by: <ID>` in the description and report it |
| Source issue | link/attachment URL or `Source:` line in description | |
| Parent (Story → project/parent issue) | `projectId` or `parentId` | Ask which, if the 3rd argument is given |

## Duplicate search

- Issues in the team with label `ai-planned` whose description contains the source ID.
- Children of the given parent.
- Title query using distinctive words from each Story title, excluding completed/canceled states.
