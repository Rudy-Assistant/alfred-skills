---
task: implement-robin-bridge
status: pending
priority: high
created: 2026-03-29T01:30:00Z
created_by: alfred
---

# Implement robin_bridge.py on Oracle

## What
Build the Robin Bridge agent that polls alfred-skills/docs/robin-tasks/ for pending tasks, claims them, executes them, and writes results.

## Why
Alfred can create task files in the repo, but Robin has no code yet to poll and execute them. This is the critical missing link in Alfred-to-Robin delegation.

## Spec
See rudy-workhorse/docs/robin-bridge-spec.md (PR #2) for the full Oracle-side specification.

## Implementation Outline
1. New file: rudy/agents/robin_bridge.py
2. Poll GitHub API: GET /repos/Rudy-Assistant/alfred-skills/contents/docs/robin-tasks/
3. For each .md file with status: pending - parse YAML frontmatter, update status to claimed, execute task steps, update status to completed or failed with result
4. Proactive schedule when no tasks pending: health check (5 min), token freshness (hourly), security sweep (daily 3 AM), model update check (weekly)
5. Register with rudy/agents/runner.py

## Acceptance Criteria
- Robin picks up a pending task within 5 minutes
- Robin updates task status in the repo
- Robin writes execution results
- Robin falls back gracefully if GitHub is unreachable

## Result
(Robin fills this in when complete)
