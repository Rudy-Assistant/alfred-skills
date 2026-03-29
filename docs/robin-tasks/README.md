# Robin Tasks

Task files Alfred creates for Robin to execute on Oracle.

## Protocol
1. Alfred creates: `YYYY-MM-DD-{task-slug}.md`
2. Robin polls every 30 minutes
3. Robin claims (status: pending → claimed)
4. Robin executes locally
5. Robin fills Result section (status: completed/failed)
6. Alfred reads result at next session start

## Status Values
- **pending** — Waiting for Robin
- **claimed** — Robin working on it
- **completed** — Done (see Result)
- **failed** — Could not complete (see Result for reason)
