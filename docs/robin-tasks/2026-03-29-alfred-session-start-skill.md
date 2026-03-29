---
task: alfred-session-start-skill
status: pending
priority: medium
created: 2026-03-29T01:30:00Z
created_by: alfred
---

# Create Alfred Session-Start Skill

## What
Build a skill for alfred-skills that Alfred runs at the beginning of every Cowork session to restore context and check for updates.

## Why
Alfred is ephemeral. Each Cowork session starts fresh. But Alfred has persistent state in git and Notion. A session-start skill would: read CLAUDE.md for behavioral directives, check Bat Family Directives in Notion for new standing instructions, check robin-tasks for completed/failed tasks and process results, read the latest Alfred Session Log to know what happened last time, check Gmail for pending requests from Batmans, and write a session-start entry to the session log.

This makes Alfred self-bootstrapping: even after a crash, the next session picks up where the last one left off.

## Implementation
Create /skills/session-start/SKILL.md in alfred-skills with step-by-step protocol, Notion page IDs, GitHub paths, Gmail search queries, and session log template.

## Note
This is a documentation/skill task. Alfred can do this one himself in a future session.

## Result
(Alfred fills this in when implemented)
