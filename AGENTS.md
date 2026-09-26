# Keep

## Map and setup

`keep/api/` and `keep/cli/` expose the Python service/CLI; `keep/providers/` integrates external systems; `tests/` holds unit, integration, and browser cases. `keep-ui/` is the Next.js interface, with routes in `app/` and shared features/components alongside them. `ee/` has separate licensing; preserve that boundary. `docs/development/getting-started.mdx` describes service setup and migrations.

Python supports >=3.11,<3.14; backend CI uses 3.11. Use Poetry and the lockfile: `poetry install --no-interaction --no-root --with dev` for CI-style tests. For the installed `keep` CLI, use `poetry install --with dev`. UI CI uses Node 20 and `npm ci` from `keep-ui/`; `npm run dev` starts the UI there. `docker compose -f docker-compose.dev.yml up` starts the development stack, requiring Docker and reviewed local configuration. Server startup applies migrations, so isolate its database/state and external providers before running it.

## Checks

Backend CI runs Ruff and `poetry run coverage run --omit='*/test*' --branch -m pytest --timeout 20 -n auto --non-integration --ignore=tests/e2e_tests/`. Use an affected test path with the same non-integration selection for iteration; plain pytest also selects integration tests. Use `poetry run ruff check keep` for the source lint gate; retain surrounding Black/isort conventions.

From `keep-ui/`, use `npm test -- --runInBand`, `npm run typecheck`, and `npm run lint` as relevant. The declared `npm run build` wrapper prints the entire process environment; do not run it with inherited credentials. Use its direct build equivalent, `NODE_OPTIONS=--max-old-space-size=8192 npm exec -- next build`, to avoid that environment dump. Browser tests require a running isolated stack and Playwright browsers (`poetry run playwright install`). Provider/integration tests can require Docker services or external accounts; confirm their fixtures and target before running them. Never use production incident/alert data or credentials as test fixtures. Keep API models, migrations, UI contracts, and workflow schema changes coordinated.

## Finishing work

Follow the nearby implementation and keep changes within the requested scope. Carry authorized changes through the relevant checks, fixing failures caused by the change. For a bug, reproduce the affected behavior and add a focused regression check when useful. Make routine reversible choices without another approval; ask only when missing information materially changes correctness, scope, or authorization, and name the exact source of any blocking rule. Report what changed, checks actually run, and concrete unverified behavior; repeat checks when new edits or evidence warrant it.
