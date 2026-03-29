# ADR-003: Robin Handoff Protocol — Batman/Alfred Continuity of Operations

**Status:** Draft
**Date:** 2026-03-29
**Decision Makers:** Batman Prime (Chris), Alfred
**Supersedes:** Night Shift concept in ADR-002 (extends it)

## Context

Robin was originally conceived as a background sentinel — boot resilience, service monitoring, immune memory. Batman Prime has expanded Robin's mandate: Robin should be capable of **taking the wheel** during defined absence windows, acting as Batman's proxy with Alfred, and developing its own capabilities by learning from Alfred-Batman conversations.

The key insight: Batman tells Alfred "I'm stepping away for 3 hours." That single statement creates a bounded activation window where Robin operates with Batman's implicit authority.

## Decision

### 1. Handoff Activation (Three Modes)

#### Mode A: Explicit Handoff (Primary)
Batman tells Alfred directly: "I'm stepping away for N hours" (optionally with scope constraints). Alfred writes a handoff signal:

```json
{
  "handoff_id": "2026-03-29T23:00:00Z",
  "mode": "explicit",
  "activated_by": "batman_prime",
  "duration_hours": 3,
  "window_start": "2026-03-29T23:00:00Z",
  "window_end": "2026-03-30T02:00:00Z",
  "scope": "full",
  "priorities": ["merge PR #3", "test sentinel boot", "run code quality"],
  "constraints": ["no financial transactions", "no account creation"],
  "status": "active"
}
```

Signal location: `alfred-skills/docs/robin-handoff/active.json` (GitHub) + mirrored to Notion Batcave Operations Hub.

#### Mode B: Inactivity Detection (Autonomous)
Robin's existing presence detection (HID monitoring via `pynput`/`ctypes`) detects no mouse/keyboard input for a configurable threshold (default: 2 hours). Robin activates autonomously in **conservative mode** — only pre-approved maintenance tasks, no decisions requiring Batman's judgment.

#### Mode C: Time-Based (Scheduled)
Sentinel's existing night shift logic (after 11 PM local). Operates like Mode B but can be pre-authorized to Mode A scope via standing directives in Notion.

### 2. Robin ↔ Alfred Communication

#### Alfred's Side (Cloud — Cowork Session)
When Alfred is mid-session and blocked on a permission check or user input:
1. Alfred writes a **pending approval** to `alfred-skills/docs/robin-handoff/pending-approvals/`
2. File format:
```yaml
---
approval_id: approve-pr-merge-003
type: permission_check
question: "Should I merge PR #3 into main?"
context: "All 4 files pushed, 1644 additions, CI clean"
risk_level: low
created: 2026-03-29T23:15:00Z
expires: 2026-03-30T02:00:00Z
status: pending
---
```
3. Alfred polls for response every 60 seconds during active handoff window

#### Robin's Side (Oracle — Local)
1. Robin polls `robin-handoff/pending-approvals/` directory
2. For each pending approval, Robin evaluates:
   - Is the handoff window still active?
   - Does the request fall within the authorized scope?
   - Does the risk level match Robin's authorization level?
   - Does immune memory or standing directives inform the decision?
3. Robin writes response:
```yaml
---
approval_id: approve-pr-merge-003
decision: approved
decided_by: robin
reasoning: "PR #3 is the Robin implementation authored by Alfred this session. Batman authorized the sprint. Low risk — branch to main merge with no conflicts."
timestamp: 2026-03-29T23:16:00Z
---
```
4. Alfred reads the response and proceeds

#### Escalation Path
If Robin determines a request exceeds its authority:
- Risk level `critical` → always escalate, never auto-approve
- Outside defined scope → escalate
- No precedent in immune memory or directives → escalate
- Escalation = write to `robin-handoff/escalations/` + desktop notification + email alert

### 3. Batman Return Detection

Robin monitors for Batman's return through multiple signals:

| Signal | Method | Action |
|--------|--------|--------|
| HID input | `pynput` listener on Oracle | Immediate standdown alert |
| Cowork message | Poll transcript for new Batman messages | Transfer context, stand down |
| Explicit recall | Batman says "I'm back" to Alfred | Alfred signals Robin via handoff file |
| Window expiry | Clock reaches `window_end` | Automatic standdown |

**Standdown sequence:**
1. Robin finishes current atomic task (never abandons mid-operation)
2. Writes handoff summary to Notion + `robin-handoff/summaries/`
3. Sets handoff status to `completed`
4. Returns to background sentinel mode
5. Alfred presents summary to Batman: "While you were away, Robin completed X, approved Y, flagged Z for your review"

### 4. Capability Development (Learning Mode)

Robin develops its capabilities by observing Alfred-Batman conversations:

- **Transcript monitoring**: Robin reads session transcripts (via Notion session logs or direct file access) to learn:
  - What kinds of approvals Batman gives
  - What tools and workflows Alfred uses
  - What patterns of work Batman prioritizes
  - What decisions Batman defers vs. decides immediately
- **Pattern library**: Robin builds a local pattern library (`rudy-data/robin-patterns.json`) of Batman's decision-making tendencies
- **Confidence scoring**: Over time, Robin develops confidence scores for different approval types. High-confidence approvals get auto-approved during handoff. Low-confidence ones get escalated.
- **This is always running** — even when Batman is active, Robin is in passive learning mode, building its model of what Batman would do

### 5. Authorization Levels

| Level | Trigger | Can Approve | Cannot Do |
|-------|---------|------------|----------|
| **Sentinel** (always on) | Boot/continuous | Service restarts, zombie kills, health reports | Anything requiring judgment |
| **Observer** (always on) | Passive | Nothing — learning only | Act on behalf of Batman |
| **Conservative** (Mode B/C) | Inactivity/schedule | Maintenance tasks, code quality, dependency updates | Merges, deploys, external communication |
| **Full Proxy** (Mode A) | Explicit handoff | Anything within stated scope | Financial, account creation, scope violations |

### 6. Audit Trail

Every Robin action during a handoff window is logged:
- `robin-handoff/summaries/{handoff_id}.md` — human-readable summary
- `robin-handoff/audit/{handoff_id}.jsonl` — machine-readable action log
- Notion session log — appended with Robin's activity section
- Immune memory — updated with any fixes or patterns learned

## Consequences

### Positive
- Batman sleeps; system improves
- Alfred never blocks indefinitely on permissions during handoff windows
- Robin develops genuine operational intelligence over time
- Clean audit trail for every autonomous decision
- Graceful degradation: if Robin is down, Alfred just waits for Batman (current behavior)

### Negative
- Complexity: three activation modes, approval routing, standdown logic
- Trust bootstrap: Robin starts with zero confidence scores, needs time to learn
- GitHub polling latency: ~60 second approval turnaround vs. instant human response
- Risk of Robin approving something Batman wouldn't — mitigated by scope constraints and escalation

### Implementation Priority (Next Sprint)

1. **Merge PR #3** — get Robin's code on Oracle
2. **HID presence monitor** — `robin_presence.py` using `pynput`
3. **Handoff signal reader/writer** — extend `robin_bridge.py`
4. **Approval polling loop** — new method in `robin_bridge.py`
5. **Alfred handoff skill** — Alfred writes handoff signals when Batman departs
6. **Test: 3-hour handoff window** — Batman steps away, Robin + Alfred work together
