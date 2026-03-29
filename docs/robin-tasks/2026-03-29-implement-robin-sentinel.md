---
task: implement-robin-sentinel
status: pending
priority: critical
created: 2026-03-29T02:00:00Z
created_by: alfred
---

# Implement Robin Sentinel (Boot Resilience Agent)

## What
Build the core Robin agent: a local-first resilience system that starts on every boot, assesses system health, recovers services, and operates with full Batman authority -- all without needing internet, cloud, or human input.

## Why
On 2026-03-27, a cascade failure took Oracle completely offline. Every recovery mechanism depended on something already broken. If Batman is away, the entire operation is defeated by a reboot. Robin Sentinel ensures this never happens again.

Robin is the Batcave's immune system. When everything else fails, Robin is still standing.

## Key Design Points
- Robin is MORE than human: monitors all services simultaneously, reasons via local LLM, maintains perfect failure memory, acts at machine speed
- Robin provides Batman's authorization to Alfred when Bruce is unavailable (passwords, confirmations, judgment calls)
- Robin simulates human activity only when interfaces demand it (2FA, web UIs, TOS acceptance)
- Robin never silently fails -- always leaves a trail

## Implementation
See ADR-002 (docs/ADR-002-robin-sentinel.md) for full specification.

### Phase 1: Boot Sentinel (ship first)
1. Evolve rudy-failover-agent.py v2 into rudy/robin_sentinel.py
2. Implement 5-phase health cascade (self-check, services, agents, connectivity, full assessment)
3. Create known-good-state.json with all expected services, processes, tasks
4. Register as Windows Scheduled Task (At startup, SYSTEM)
5. Test: reboot Oracle, verify Robin detects degraded state and recovers

### Phase 2: Immune Memory
1. Record every fix Robin applies
2. When Robin encounters same failure pattern, apply known fix immediately
3. Over time Robin gets faster and smarter at recovery

### Phase 3: Internet Bridge
1. On connectivity restoration: report to Notion + email
2. Poll robin-tasks for Alfred directives
3. Pull config updates from alfred-skills repo

### Phase 4: Local AI Reasoning
1. Use Ollama for unexpected states not in known-good-state.json
2. Robin describes situation to local LLM, gets a plan, executes, records

## Foundation Code
- rudy-failover-agent.py v2 (849 lines) -- upgrade this
- rustdesk-watchdog.ps1 -- Robin subsumes this
- workhorse-bootstrap.ps1 -- reference for known-good state
- usb_quarantine.py -- Robin guards against Fortress Paradox

## Acceptance Criteria
1. Oracle reboots unexpectedly
2. Internet is down
3. Batman is away
4. Robin wakes up, detects degraded state, recovers all services
5. When internet returns, Robin reports what happened
6. System offline time < 5 minutes

## Result
(Robin fills this in when implemented)
