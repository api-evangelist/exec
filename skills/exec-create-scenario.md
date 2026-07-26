---
name: Create an AI roleplay scenario with Exec Scenario Studio
description: Kick off an asynchronous scenario-creation job, poll it to completion (or receive a callback), and cancel if needed — using idempotency to avoid duplicates.
api: openapi/exec-openapi-original.yml
operations: [createScenarioJob, getScenarioJob, cancelScenarioJob, createScenarioStudioSession]
---

# Create an AI roleplay scenario (Scenario Studio)

Use the Exec API to generate a custom roleplay scenario for a workspace member.

## Auth
All requests use `Authorization: Bearer exec_live_...` (see `authentication/exec-authentication.yml`). Keys have full workspace admin access.

## Steps
1. **Start the job** — `POST /scenario-studio/jobs` (`createScenarioJob`). Required: `user_email` (must be a workspace member) and `prompt` (≤10,000 chars). Optional: `context` (Markdown background), `scenario_slug` (remix an existing scenario), and `callback_url` (+ `callback_headers`) to receive a webhook POST on completion.
2. **Set idempotency** — pass a stable `request_id` in the body. If a job with that id already exists, Exec returns the existing job instead of creating a duplicate (see `conventions/exec-conventions.yml`). Reuse the same `request_id` on retries.
3. **Poll for completion** — `GET /scenario-studio/jobs/{job_id}` (`getScenarioJob`) until `status` is terminal. Jobs typically finish within ~5 minutes. If you supplied a `callback_url`, wait for the POST instead of polling.
4. **Cancel if needed** — `DELETE /scenario-studio/jobs/{job_id}` (`cancelScenarioJob`) cancels a job that is still queued or processing.
5. **Interactive alternative** — `POST /scenario-studio` (`createScenarioStudioSession`) returns a URL the user visits to build the scenario in the Scenario Studio UI.

## Rules
- Respect rate limits: scenario creation is capped at 10 requests/minute; a 429 returns a `retry_after` field — back off and retry (`rate-limits/exec-rate-limits.yml`).
- Errors use the envelope `{ "error": { "type", "code", "message" } }` (`errors/exec-error-codes.yml`).
