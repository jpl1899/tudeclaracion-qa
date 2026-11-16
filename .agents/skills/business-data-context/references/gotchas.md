# Measured Gotchas

> Loaded by: `business-data-context`, when reading this project's data map could mislead a competent session. Every row was measured on the date given against the source it names. Rows that stop being true move to §"No longer true"; they are never deleted. Proposed and applied per `agentic-qa-core/references/skill-refinement-protocol.md`.

## 1 · Reading the map

| # | Gotcha | Symptom | Fix | Measured | Verified against |
|---|---|---|---|---|---|
| 1 | A declaration `estado` is a progress marker, not proof of the file: PADI files are recomputed from `asiento` + `plan_cuentas` / `mapeo_1376` every time the review page opens | A re-download after a plan or mapping edit differs from the file already filed, while `estado` still says `generada` | Assert on the recomputed file content, never on `estado` alone | 2026-11-16 | `src/modules/exporter/`, `src/app/dashboard/declaraciones/` (code only; re-confirm on staging) |
| 2 | `declaracion_1376.estado` survives a period re-import with replace | 1376 shows `generada` over new data | Treat it as a suspected defect; after a replace, check the 1376 status explicitly | 2026-11-16 | `supabase/migrations/20260923100806_crear_declaracion_1376.sql`, `src/modules/declaraciones/periodos.ts` (code only; re-confirm on staging) |
| 3 | `empresa.estado` and `declaracion.estado = con_errores` have no writer in code or triggers | Tests waiting for those states never see them | Do not design a test around a transition into them until a writer exists | 2026-11-16 | `supabase/migrations/20260712000000_baseline_cuenta_empresa.sql`, `supabase/migrations/20260717170000_crear_declaracion.sql`, `src/` (code only) |
| 4 | Status UPDATEs run from the browser through the Supabase client, guarded only by RLS | A crafted request can set a status the UI never would | Cover status writes and cross-account access at API level, not only through the UI | 2026-11-16 | `src/app/dashboard/declaraciones/[periodo]/declaracion-detalle-client.tsx`, RLS policies in `supabase/migrations/` (code only) |
| 5 | The `plan_cuentas` unique constraints ignore the `declaracion` column | Two rows for the same account in different declarations collide, or a test expecting both to coexist fails | Read the constraint in the migrations before building plan test data | 2026-11-16 | `supabase/migrations/20261001121317_plan_cuentas_declaracion.sql` (code only; re-confirm on staging) |

## 2 · No longer true

_(empty)_
