# Measured Gotchas

> Loaded by: `business-e2e-context`, when reading this project's e2e map could mislead a competent session. Every row was measured on the date given against the source it names. Rows that stop being true move to §"No longer true"; they are never deleted. Proposed and applied per `agentic-qa-core/references/skill-refinement-protocol.md`.

## 1 · Reading the map

| # | Gotcha | Symptom | Fix | Measured | Verified against |
|---|---|---|---|---|---|
| 1 | A journey step marked EXTERNAL DEPENDENCY (inbox, Turnstile, Google OAuth, BCU, PADI upload) cannot be driven by a plain UI test | A UI test stalls or flakes at that step | Seed, stub or bypass that step (pre-confirmed test user, stored auth state, mocked BCU) and test the dependency on its own | 2026-11-16 | 6 of 10 journeys in the e2e map contain one, read from code |
| 2 | Every route, step and message in the map was read from code; no journey was executed on staging | A step or message differs on the deployed build | Confirm a journey on staging before writing an assertion against its exact text | 2026-11-16 | 0 journeys executed when the map was generated; open items in `discovery-gaps` |
| 3 | The `?redirect=` and `?error=` query values do not do what the middleware and the confirm route imply | A deep link always lands on `/inicio`; `?error=verification-failed` and `?error=expired` show no message | Treat them as suspected defects, not as expected behaviour, until confirmed | 2026-11-16 | `rg` found no reader of `redirect` and no consumer of `verification-failed` / `expired` in `src/app/login` and `src/app/forgot-password` |

## 2 · No longer true

_(empty)_
