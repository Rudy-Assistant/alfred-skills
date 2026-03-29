---
task: upgrade-pat-scopes
status: pending
priority: high
created: 2026-03-28T23:00:00Z
created_by: alfred
---

# Upgrade GitHub PAT Scopes for Rudy-Assistant

## What
The current GitHub Personal Access Token (fine-grained) for Rudy-Assistant lacks sufficient scopes to create Pull Requests via the API on the `rudy-workhorse` repo.

## Why
Alfred attempted to create PR #2 via the GitHub API and received:
```
403 - Resource not accessible by personal access token
```
Alfred had to fall back to creating the PR through Chrome's authenticated web session. This workaround works but is fragile and slow. Alfred needs API-level PR creation to operate autonomously.

## Steps (suggested)
1. Log into GitHub as Rudy-Assistant
2. Navigate to Settings > Developer settings > Personal access tokens > Fine-grained tokens
3. Edit the existing token (or create a new one) for `rudy-workhorse` repo
4. Ensure these permissions are granted:
   - **Contents**: Read and write
   - **Pull requests**: Read and write
   - **Metadata**: Read-only
5. Copy the new token value
6. Update any environment variables or configs that reference the old token
7. Test by making a simple API call: `GET /repos/Rudy-Assistant/rudy-workhorse/pulls`

## Acceptance Criteria
- Alfred can create PRs on rudy-workhorse via `POST /repos/Rudy-Assistant/rudy-workhorse/pulls`
- Alfred can create PRs on alfred-skills via the same pattern
- No 403 errors on PR-related API endpoints

## Result
(Robin fills this in when complete)
