---
name: Assign a scenario and track team performance
description: Find a scenario, assign it to a workspace member, then read their sessions and the scenario's analytics to track progress.
api: openapi/exec-openapi-original.yml
operations: [listScenarios, checkScenarioAccess, grantScenarioAccess, assignScenario, listSessions, getSession, getScenarioAnalyticsSummary, getScenarioAnalyticsParticipants]
---

# Assign a scenario and track performance

## Auth
`Authorization: Bearer exec_live_...` (full workspace admin).

## Steps
1. **Find the scenario** — `GET /scenarios` (`listScenarios`) and pick the target by name/slug. Both UUID and slug work as the `scenario_id` path parameter.
2. **Check / grant access** — `GET /scenarios/{scenario_id}/access` (`checkScenarioAccess`) to see a user's permission level; `POST /scenarios/{scenario_id}/access` (`grantScenarioAccess`) to grant it.
3. **Assign it** — `POST /scenarios/{scenario_id}/assignments` (`assignScenario`) to give a user the scenario as a task/homework. The user is notified and progress is tracked. List existing assignments with `GET /scenarios/assignments` (`listAssignments`).
4. **Read results** — `GET /sessions` (`listSessions`) for roleplay sessions (inline user + scenario), then `GET /sessions/{session_id}` (`getSession`) for full detail (score, rank, transcript, feedback).
5. **Analytics** — `GET /scenarios/{scenario_id}/analytics/summary` (`getScenarioAnalyticsSummary`) for aggregate metrics, and `GET /scenarios/{scenario_id}/analytics/participants` (`getScenarioAnalyticsParticipants`) for a per-user performance table.

## Rules
- All list endpoints are page-number paginated (`page`, `page_size`; response `pagination` object) — see `conventions/exec-conventions.yml`.
- Handle 429 with `retry_after`; handle the `{ "error": {...} }` envelope on 4xx.
