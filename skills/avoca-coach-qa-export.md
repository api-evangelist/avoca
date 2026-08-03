---
name: Export Avoca Coach QA evaluations
description: Pull scored Coach (QA) call evaluations and join them to their rubrics from the Avoca Enterprise API.
api: openapi/avoca-openapi-original.json
operations: [listCoachRubrics, listCoachCalls, getCoachCall]
---

# Export Avoca Coach QA evaluations

Export quality-assurance scoring from the Avoca Enterprise API. Requires the
`read:coach` scope. Base `https://enterprise-api.avoca.ai`, bearer API-key auth.

## Steps

1. **Load rubrics** — `listCoachRubrics` (`GET /api/coach/rubrics`). Rubric items
   provide the `key → name` mapping for the `evaluation_answers` keys returned on
   each evaluated call.
2. **List evaluations** — `listCoachCalls` (`GET /api/coach`), newest first. Each
   row includes `score`, `awarded_points`, `max_available_points`,
   `evaluation_answers`, and the `rubric_id` for joining back to step 1.
3. **Drill in (optional)** — `getCoachCall` (`GET /api/coach/{id}`) for a single
   evaluation by its numeric id.

## Rules

- Join `rubric_id` from the call to the rubric list to resolve human-readable
  answer names.
- Missing `read:coach` scope returns `403`; unknown id returns `404`.
