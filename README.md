# PNL Construction

Complete source for the PNL workforce, attendance, payroll and project-finance system.

- **Open the system:** https://levigamage06.github.io/pnl-construction/
- **Live application:** https://pnl-construction.inquiries-quidedge-au.workers.dev/
- **Application source:** [app/](app/)
- **Implementation and validation notes:** [app/README.md](app/README.md)

## Repository layout

| Path | Purpose |
| --- | --- |
| `index.html` | Existing automatic GitHub Pages redirect; users go straight to the live system. |
| `app/index.html`, `app/*.js`, `app/*.css` | Complete browser UI, financial engine and retained-history logic. |
| `app/cloud/worker.mjs` | Cloudflare Worker HTTP API and static-asset server. |
| `app/cloud/schema.sql` | Append-only D1 database schema and guards. |
| `app/cloud/wrangler.jsonc` | Existing deployment settings and database bindings. IDs are configuration, not API credentials. |
| `app/cloud/build-assets.py` | Builds the content-hashed browser bundle and Worker modules. |
| `app/test-*.cjs`, `app/cloud/test-*` | Calculation, UI renderer, storage, Worker, cache and schema regression checks. |
| `app/cloud/qa-server.mjs` | Disposable local preview using in-memory databases and fictional fixtures; never deploy it to production. |
| `app/cloud/offsite-backup.py` | Optional independent backup export helper; no backup schedule is configured by this repository. |

## Build and check

Use Python 3 and Node.js 24 or later. No npm packages are required for these checks.

```sh
cd app
python3 cloud/build-assets.py
node test-engine.cjs
node test-finance.cjs
node test-attendance.cjs
node test-quality.cjs
node test-smooth-ui.cjs
node test-views.cjs
node test-live.cjs
node test-storage.cjs
node test-vault.cjs
node cloud/test-worker.mjs
node cloud/test-performance.mjs
python3 cloud/test-schema.py
```

## Disposable local preview

```sh
cd app
npm run dev
```

Open the local URL printed by the preview server. It uses fictional data in temporary in-memory databases, not the company database. Preview fixtures disappear when the server stops. `mobile-check.html` provides test viewport sizes.

## Production deployment

The complete application needs the Worker API and its D1 bindings; GitHub Pages alone cannot run this backend. Publishing source to GitHub does not deploy a new Worker or move database records into GitHub.

After editing browser source, rebuild from `app/` with `python3 cloud/build-assets.py`. Use Cloudflare's deployment tooling with `app/cloud/wrangler.jsonc`, keeping the existing DB and MIRROR bindings. Run schema creation only when provisioning a new database; do not reset an existing company database. Keep authentication credentials in your own environment or credential manager, never in this public repository.

The root Pages redirect is retained. The historical Pages workflow under `app/.github/` is included as source documentation and is not active at the repository root. Its redirect copy is at `app/github-pages/index.html`.

## Data and access

This repository contains code, schema, synthetic tests and deployment configuration. It does **not** contain real employee records, attendance, payroll exports, database snapshots or API tokens.

The current live system has public read/write access, explicitly selected by its owner. Saved database revisions are append-only; employee/project archival and explicit corrections retain previous revisions. The mirror shares the same Cloudflare account. Independent offsite backup is not configured. Free service quotas apply.

Payroll and settlement calculations are operational tools with explicit review requirements. Special APIT cases need reviewed manual amounts. The app does not execute bank transfers or submit official statutory returns. Passing regression tests is not universal bug-free or legal/accounting certification.
