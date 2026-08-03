---
name: Pull Avoca calls and transcripts
description: Retrieve a team's calls and their transcripts from the Avoca Enterprise API for QA, analytics, or CRM sync.
api: openapi/avoca-openapi-original.json
operations: [listCalls, getCall, getCallTranscript, getLatestCallByPhone]
---

# Pull Avoca calls and transcripts

Use the Avoca Enterprise API (base `https://enterprise-api.avoca.ai`) to pull call
records and transcripts. Authentication is a bearer API key in the format
`avoca_<64 hex characters>` sent as `Authorization: Bearer <API_KEY>`. Requires the
`read:calls` scope (and `read:transcripts` for transcripts). Multi-team keys must
set the `x-team-id` header (v0 endpoints).

## Steps

1. **List calls** — `listCalls` (`GET /api/calls`). The returned columns honor the
   team's configured column allowlist. Page through results as needed.
2. **Get one call** — `getCall` (`GET /api/calls/{id}`) for full metadata:
   `call_reason`, `call_outcome`, `is_booked`, `booking_result`, `is_transferred`.
3. **Get the transcript** — `getCallTranscript` (`GET /api/calls/{id}/transcript`).
   Needs the `read:transcripts` scope.
4. **Look up by phone (optional)** — `getLatestCallByPhone`
   (`GET /api/calls/latest-by-phone`) to find the most recent call for a number.

## Rules

- Missing scope returns `403`; a bad key returns `401`; unknown id returns `404`
  (see `errors/avoca-problem-types.yml`).
- Pre-call transfers (`listPreCallTransfers`) have `duration_seconds: 0` because
  the AI never engaged — handle them separately.
