# tudeclaracion-qa

**QA automation for [tuDeclaracion](https://github.com/jpl1899/tudeclaracion).**

This repo is kept separate from the app on purpose. The app and its test automation have different lifecycles, and these tests run against a **deployed environment** (staging), not against local source.

🔗 **System under test:** https://tudeclaracion-staging-pus8.onrender.com/

---

## Approach

- **Two-repo topology:** the app repo owns the code, and this repo owns the proof that it works.
- **Story-driven tests:** each test traces back to a Jira story and its acceptance criteria.
- **Layered framework:** fixtures, page objects (`tests/components/ui`), API clients (`tests/components/api`), and test data factories (`tests/data`).
- **CI pipelines** in GitHub Actions: smoke, sanity, regression, and unit runs (`.github/workflows/`).
- **Reporting** with Allure.
- **AI-assisted QA:** agent skills cover the QA lifecycle, from test plan to regression suite to release sign-off, with a human approving each step.

## Status

🚧 **Work in progress.** The framework and CI are in place. Coverage is growing story by story. Current specs: dashboard (E2E) and user session (auth integration).

## Running

```bash
bun install
bunx playwright install chromium
cp .env.example .env   # set the base URL and test credentials
bun run test
```

## Tech stack

Playwright, TypeScript, Bun, Allure, GitHub Actions.

Built on top of an [agentic QA boilerplate](https://github.com/upex-galaxy/agentic-qa-boilerplate). The original README lives in `docs/BOILERPLATE.md`.
