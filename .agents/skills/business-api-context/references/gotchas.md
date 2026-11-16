# Measured Gotchas

> Loaded by: `business-api-context`, when reading this project's api map could mislead a competent session. Every row was measured on the date given against the source it names. Rows that stop being true move to §"No longer true"; they are never deleted. Proposed and applied per `agentic-qa-core/references/skill-refinement-protocol.md`.

## 1 · Reading the map

| # | Gotcha | Symptom | Fix | Measured | Verified against |
|---|---|---|---|---|---|
| 1 | The map has no `openapi:` sources: the SUT has no OpenAPI spec | A staleness check finds no contract to diff | Detect staleness only through the repo-path, `route:`, `db:` and `rpc:` sources | 2026-11-16 | the map's `data-sources`; no spec in either repo (`api/openapi-types.ts` here is the boilerplate stub) |
| 2 | Endpoints are named by supabase-js method, table or RPC, not by HTTP path | Searching the map for a REST path finds nothing | Look up the table or RPC name; only `/auth/callback`, `/auth/confirm` and the Server Actions are Next.js endpoints | 2026-11-16 | 2 route handlers and 4 Server Actions in `../tudeclaracion/src/`; everything else is direct supabase-js |
| 3 | RLS policy content was read from migrations, not from the live DB | A policy behaves differently on staging than the map says | Treat the policy tables as unverified until checked on staging (`[DB_TOOL]` or a two-account request) | 2026-11-16 | `../tudeclaracion/supabase/migrations/` (27 files), no DB access |

## 2 · No longer true

_(empty)_
