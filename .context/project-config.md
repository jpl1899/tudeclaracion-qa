# Project Configuration

> Project: tudeclaracion-qa (system under test: tuDeclaracion.com)
> Generated: 2026-11-14

## Repositories

Single full-stack application: frontend and backend live in the same repository.

| Repository | URL | Branch | Purpose |
|------------|-----|--------|---------|
| `../tudeclaracion` (FE + BE) | https://github.com/jpl1899/tudeclaracion | main (default), staging (deployed to staging) | Web application and API (Next.js Server Actions + Supabase) |
| `tudeclaracion-qa` (this repo) | n/a | main | QA repository: KATA automation, QA context, Jira sync |

## Tech Stack

### Frontend
- Framework: Next.js ^15.5 (App Router) with React ^19.2
- Language: TypeScript ^5.9 (`strict: true`)
- Styling: Tailwind CSS 3 + Radix UI primitives + shadcn-style components (`src/components/ui`), `lucide-react` icons
- State: local React state (no global store library in `package.json`)
- Content: `react-markdown` + `remark-gfm` render the in-app Ayuda module

### Backend
- Framework: Next.js Server Actions (`src/app/**/actions.ts`), route handlers (`src/app/auth/callback`, `src/app/auth/confirm`), middleware (`middleware.ts`); business logic in `src/modules/`
- Language: TypeScript; Supabase Edge Function in Deno (`supabase/functions/send-email`)
- ORM: none. Direct `@supabase/supabase-js` / `@supabase/ssr` queries plus Postgres RPCs (`security invoker`): `insertar_asientos`, `crear_empresa_con_plan_cuentas`, `editar_empresa_con_plan_cuentas`, `guardar_mapeo_1376`, `borrar_periodo`
- Runtime and package manager: Bun (`bun.lock`, scripts run with `bun`)

### Database
- Type: PostgreSQL
- Provider: Supabase (Auth, Postgres, Edge Functions); schema authority = `../tudeclaracion/supabase/migrations/` (27 migrations, baseline 2026-07-12)
- Isolation model: Row Level Security on every business table, scoped through `empresa.cuenta_id = auth.uid()`
- Access: `dbhub` MCP is configured in `.mcp.json` (connection variables: `DBHUB_TYPE`, `DBHUB_HOST`, `DBHUB_PORT`, `DBHUB_DATABASE`, `DBHUB_USER`, `DBHUB_PASSWORD`) but NOT connected in this session (`DBHUB_PASSWORD` is unset; MCP reported CONNECTION_CLOSED)

### Infrastructure
- Cloud / hosting: Render (staging; `render.yaml` blueprint, service `tudeclaracion-staging`, free plan, branch `staging`, `bun install && bun run build` then `bun run start`). The target repo's `vercel.json` (`framework: nextjs`) and `.context/deploy/render/README.md` describe Vercel as the SRS default and Render as "Plan B"; the production host is not confirmed (see Discovery Gaps)
- CI/CD: none in the repository (no `.github/workflows`); only local Husky hooks (see Project Assessment in `.agents/instructions/agent-project.md`)
- Transactional email: Resend, through the Supabase Auth "Send Email" hook (Edge Function `send-email`, ADR-0002)
- External data source: BCU (Banco Central del Uruguay) SOAP web service for exchange rates (`src/modules/cotizacion/fetch-cotizacion-bcu.ts`), cached in table `cotizacion_bcu_cache`
- Monitoring: none found in `package.json` or `src/` (an `.env.sentry-build-plugin` file exists in the target root; it was not opened and no Sentry package is declared)

## Environments

| Environment | URL | Purpose | Access |
|-------------|-----|---------|--------|
| Local | http://localhost:3000 | Development (`bun run dev`) | Direct; needs the Supabase variables of `.env.example` |
| Staging | https://tudeclaracion-staging-pus8.onrender.com | Pre-production testing, default QA environment (`testing.default_env`) | Public URL; account sign-up through `/signup`, email confirmation via Resend |
| Production | Unknown | Live product (tuDeclaracion.com is the intended domain) | Not verified, no URL in the repository or `project.yaml` |

Routes behind login (middleware): `/inicio`, `/cuenta`, `/empresas`, `/dashboard`. Public: `/`, `/login`, `/signup`, `/forgot-password`, `/reset-password`.

## Tools and Access

- Issue tracker: Jira Cloud, site https://lk-tudeclaracion.atlassian.net (from `issue_tracker.atlassian_url` in `.agents/project.yaml`); `issue_tracker` and `issue_tracker_cli` are still `null` there
- Project key: TD
- Test management: not chosen (`testing.tms_cli` is `null`)
- Database: `dbhub` MCP configured, not connected (see above). Staging DB variables are referenced by NAME only (`DBHUB_*`); never paste values
- API contract source: none. No OpenAPI spec exists (no `openapi.*`/`swagger.*` file, no generator package) and there is no `src/app/api`; the only route handlers are `/auth/callback` and `/auth/confirm`. The "API" surface is 4 Server Actions, those 2 route handlers and Supabase PostgREST/RPC, so `bun run api:sync` has nothing to sync. Next step: `/project-context api` after discovery for the business-angle map; logged in the `infra-context` discovery-gaps
- Docs: product docs live in the target repo under `.context/` (PRD, SRS, ADR, Tax-rules, plan-de-pruebas.md, PBI) and `content/ayuda/`; no Confluence or Notion found
- Credentials: referenced only by variable name (target app variables `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY`, `SUPABASE_PUBLISHABLE_KEY`, `SUPABASE_SECRET_KEY`, `SUPABASE_JWT_SECRET`, `NEXT_PUBLIC_APP_URL` per `render.yaml`; QA test-user variables are defined by the QA repo's own env schema)

## Access Checklist

- [x] Repository read access (`../tudeclaracion` readable from this workspace)
- [ ] Database access (MCP or direct): blocked, `dbhub` not connected because `DBHUB_PASSWORD` is unset
- [ ] Issue tracker access: Jira site known, access not verified in Phase 1 (belongs to the backlog connection check)
- [ ] Staging environment reachable: URL recorded, not requested in this phase
- [x] CI/CD visibility: confirmed there is none (no workflow files, `render.yaml` only builds and starts the service)

## Discovery Gaps

- [ ] Production URL and hosting provider: `vercel.json` and `render.yaml` both exist; the SRS says Vercel is the default, staging runs on Render. Source of truth: product owner / `.context/deploy/`
- [ ] Database connection for QA: set `DBHUB_PASSWORD` (human, in the secret manager or `.env`) and restart the agent session so `dbhub` connects
- [ ] Jira access check and `issue_tracker` / `issue_tracker_cli` / `tms_cli` values in `.agents/project.yaml` are still `null`
- [ ] Monitoring and error tracking: none found; confirm whether Sentry is planned (an `.env.sentry-build-plugin` file exists in the target, not opened)
- [ ] Test accounts and seed data for staging: not documented in the repository; needed for `/test-framework-adaptation`
- [ ] Staging `staging` branch vs `main`: which branch deploys where was inferred from `render.yaml` (`branch: staging`), confirm with the team
