---
task: configure-github-mcp-token
status: pending
priority: high
created: 2026-03-28T22:00:00Z
created_by: alfred
---

# Configure GitHub MCP Token

## What
Configure the GitHub MCP server with a valid PAT so Alfred can use `mcp__github__*` tools directly.

## Why
Currently returns 'Unauthorized'. Alfred routes through Chrome JS context as workaround.

## Steps
1. Locate GitHub MCP config on the Workhorse
2. Set GITHUB_PERSONAL_ACCESS_TOKEN to valid PAT
3. Restart MCP server / Claude application
4. Verify by testing mcp__github__search_repositories

## Acceptance Criteria
- mcp__github__get_file_contents returns data instead of Unauthorized
- Alfred can read and write to both repos via MCP directly

## Result
_(Robin fills this in when complete)_
