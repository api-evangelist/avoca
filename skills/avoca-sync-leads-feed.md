---
name: Sync the Avoca leads feed
description: Incrementally pull the canonical Avoca leads feed for a team (or across an enterprise) into a warehouse or CRM using cursor pagination.
api: openapi/avoca-openapi-original.json
operations: [listTeams, listTeamLeads, listEnterpriseLeads]
---

# Sync the Avoca leads feed

Pull the canonical leads record from the Avoca Enterprise API. Requires the
`read:leads` scope (and `read:teams` to enumerate teams). Base
`https://enterprise-api.avoca.ai`, bearer API-key auth.

## Steps

1. **Discover teams** — `listTeams` (`GET /api/v1/teams`) to get every `team_id`
   the key can reach. Requires `read:teams`.
2. **Pull the single-team feed** — `listTeamLeads`
   (`GET /api/v1/teams/{teamId}/leads`). This is the historical/pull counterpart
   to the `lead.*` webhooks and includes the raw third-party payload. It is
   keyset cursor-paginated **ascending** by `(created_at, id)`.
3. **Paginate** — pass `cursor` and follow `next_cursor` until `has_more` is
   false (see `conventions/avoca-conventions.yml`). Persist the last cursor for
   the next incremental run.
4. **Enterprise-wide (optional)** — `listEnterpriseLeads`
   (`GET /api/v1/enterprise/{enterpriseId}/leads`) with an `enterprise_all_teams`
   key returns every brand's leads (each row carries `team_id`). Single-team and
   portfolio keys get `403`.

## Rules

- Test-number leads are excluded by default; pass `include_test_numbers=true` to
  include them.
- For real-time delivery instead of polling, subscribe to the `lead.created`,
  `lead.booking_created`, and `lead.completed` webhooks
  (`asyncapi/avoca-webhooks.yml`).
