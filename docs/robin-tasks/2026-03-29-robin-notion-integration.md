---
task: robin-notion-integration
status: pending
priority: medium
created: 2026-03-29T01:30:00Z
created_by: alfred
---

# Enable Robin to Read/Write Notion

## What
Give Robin direct Notion API access so Oracle can update the Batcave Operations Hub, write health logs, and read directives.

## Why
Right now only Alfred (via Cowork MCP) can write to Notion. Robin should also be able to: write health check results to the Watchdog Health Log, read Bat Family Directives for standing instructions, update the Batcave Operations Hub with Oracle status, log completed tasks and their results.

## Steps
1. Create a Notion integration at https://www.notion.so/my-integrations (Name: Robin-Workhorse)
2. Copy the integration token
3. Share the Batcave Operations Hub page with the integration
4. Share the Workhorse Command Center page with the integration
5. Store token in Windows Credential Manager via keyring
6. Create rudy/tools/notion_client.py with basic read/write functions
7. Test: Robin writes a test entry to the Watchdog Health Log

## Key Notion Page IDs
- Batcave Operations Hub: 3327d3f7-e736-81b1-8c48-d300c31a7883
- Bat Family Directives: 3327d3f7-e736-81b5-8293-faa7d9c5ed7d
- Alfred Session Log: 3327d3f7-e736-81ff-ab82-d73d2f106a61
- Workhorse Command Center: 32f7d3f7-e736-81fc-aa01-d378d347d427

## Acceptance Criteria
- Robin can read the Bat Family Directives page
- Robin can append entries to the Watchdog Health Log
- Robin can update the Batcave Operations Hub with Oracle system status
- Token stored securely in Credential Manager (not plaintext)

## Result
(Robin fills this in when complete)
