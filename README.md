# THE BIG GUYS — Recipe Costing

A single-page HPP (harga pokok produksi) and recipe-costing tool for THE BIG GUYS burger business — no build step, backed by Supabase for shared, cross-device data storage.

## Features

- **Dashboard** — ingredient/recipe counts, average HPP per unit, average margin, top-margin recipes, recent ingredient price changes.
- **Bahan Baku (Ingredients)** — CRUD with waste % (susut), category tagging, effective-price calculation, price-history tracking with a mini bar chart, a bulk-purchase → per-unit price conversion calculator, and CSV export.
- **Resep (Recipes)** — composition builder from ingredients, prep-time labor costing, automatic overhead %, manual extra costs, sell price / target-margin solver, live cost breakdown, duplicate, and CSV export.
- **Kalkulator Batch** — scale any recipe by a multiplier or a target yield, with a generated shopping list and CSV export.
- **Rencana Produksi (Production Plans)** — combine multiple recipes into a plan and get an aggregated market list and cost summary, with CSV export.
- **Pengaturan (Settings)** — hourly labor rate, overhead %, default target margin, default waste %, price rounding step, custom units, business name/currency, and dark mode.

## Data storage & access

Data (ingredients, recipes, plans, settings) is stored server-side in a Supabase Postgres database, shared across every device. Access is gated by a single shared PIN (set in Settings → *Ubah PIN Akses*):

- The frontend never talks to the `app_data`/`app_auth` tables directly — `anon`/`authenticated` have zero grants on them. All reads/writes go through three `SECURITY DEFINER` RPC functions (`get_app_data`, `set_app_data`, `change_pin`) that verify the PIN (hashed with `pgcrypto`'s `crypt()`) before touching any row.
- The entered PIN is cached in `localStorage` on that device so it isn't asked for on every visit. Deleting that value (or clearing site data) will re-prompt for the PIN.
- If the device can't reach Supabase (offline, outage), the app falls back to a local-only `localStorage` cache instead of blocking — it re-syncs on the next successful save once connectivity returns.
- The very first device to authenticate against a freshly created (empty) backend migrates whatever it already has in `localStorage` up to Supabase automatically.

The schema migration lives in Supabase itself (project "The Big Guys"); rerun/adjust it via the Supabase SQL editor or MCP `apply_migration` if you need to change it.

## Local development

No build tooling is required — it's a single static `index.html`. Serve it with any static file server, e.g.:

```bash
python3 -m http.server 8080
```

Then open `http://localhost:8080`.

## Deploying to Cloudflare Pages

This repo includes `.github/workflows/deploy.yml`, which publishes the site to Cloudflare Pages on every push to `main` or a `claude/**` branch, using [`cloudflare/pages-action`](https://github.com/cloudflare/pages-action).

To enable it, add these repository secrets (Settings → Secrets and variables → Actions):

- `CLOUDFLARE_API_TOKEN` — a Cloudflare API token with **Cloudflare Pages: Edit** permission.
- `CLOUDFLARE_ACCOUNT_ID` — your Cloudflare account ID (Cloudflare dashboard → right sidebar).

The workflow deploys to a Pages project named `thebigguys` (created automatically on first deploy).

### Alternative: connect the repo directly in the Cloudflare dashboard

No GitHub Actions or secrets needed:

1. Cloudflare dashboard → **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**.
2. Select this repository.
3. Build settings: **Framework preset: None**, **Build command: (empty)**, **Build output directory: `/`**.
4. Save and deploy.
