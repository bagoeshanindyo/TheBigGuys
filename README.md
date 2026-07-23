# THE BIG GUYS — Recipe Costing

A single-page HPP (harga pokok produksi) and recipe-costing tool for THE BIG GUYS burger business, entirely client-side (no backend, no build step).

## Features

- **Dashboard** — ingredient/recipe counts, average HPP per unit, average margin, top-margin recipes, recent ingredient price changes.
- **Bahan Baku (Ingredients)** — CRUD with waste % (susut), category tagging, effective-price calculation, price-history tracking with a mini bar chart, a bulk-purchase → per-unit price conversion calculator, and CSV export.
- **Resep (Recipes)** — composition builder from ingredients, prep-time labor costing, automatic overhead %, manual extra costs, sell price / target-margin solver, live cost breakdown, duplicate, and CSV export.
- **Kalkulator Batch** — scale any recipe by a multiplier or a target yield, with a generated shopping list and CSV export.
- **Rencana Produksi (Production Plans)** — combine multiple recipes into a plan and get an aggregated market list and cost summary, with CSV export.
- **Pengaturan (Settings)** — hourly labor rate, overhead %, default target margin, default waste %, price rounding step, custom units, business name/currency, and dark mode.

Data is persisted in `localStorage` (or a `window.storage` bridge if the hosting shell provides one).

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
