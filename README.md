# InkSpirit

AI-powered graphic t-shirt storefront. Customers generate custom designs (pet portraits, team shirts) or pick from curated collections, check out via Stripe, and orders are fulfilled on-demand through Printify.

**Stack:** Vue 3 (Vite) · Express 5 · Postgres · Stripe Checkout · Printify · OpenAI (`gpt-image-1`) · Resend

## Features

- AI-generated designs — pet portraits and team shirts, rendered via OpenAI's image model
- Curated t-shirt collections with per-size, per-color variants
- Stripe Checkout for payment, with promo code support
- Automatic fulfillment: paid orders are created in Printify via webhook
- Order lookup by order number + email, with live status
- Customer reviews, gated to verified buyers
- Transactional email (order confirmation, shipping, review request) via Resend
- SEO landing pages + sitemap/prerendering for organic discovery

## Getting Started

```bash
npm install
cp .env.example .env.local   # fill in API keys — see below
npm run dev
```

This starts Vite (`:5173`) and the Express API (`:4242`) together; Vite proxies `/api/*` to Express. Open **http://localhost:5173**.

## Commands

```bash
npm run dev      # Vite + Express, with hot reload
npm run build    # production build → dist/
npm start        # build + serve via Express (production mode)
npm test         # Vitest suite (unit + API + component)
```

## Environment Variables

Copy `.env.example` to `.env.local` and fill in:

| Variable | Description |
|---|---|
| `STRIPE_SECRET_KEY` | Stripe secret key (`sk_test_...` / `sk_live_...`) |
| `STRIPE_WEBHOOK_SECRET` | From `stripe listen` (dev) or the Stripe Dashboard (prod) |
| `PRINTIFY_API_TOKEN` | Printify personal access token |
| `PRINTIFY_SHOP_ID` | Printify shop ID |
| `PRINTIFY_WEBHOOK_SECRET` | Optional — verifies incoming Printify shipment webhooks |
| `DATABASE_URL` | Postgres connection string (order + review storage) |
| `OPENAI_API_KEY` | Powers AI pet portrait / team shirt generation |
| `FAL_KEY` | Hosts generated images so Printify can fetch them |
| `RESEND_API_KEY` | Transactional email (no-ops until set) |
| `EMAIL_FROM` | From address for transactional email |
| `SITE_URL` | Full site URL (Stripe redirects, image URLs) |
| `PORT` | Defaults to `4242` |

Optional analytics (build-time): `VITE_GA_MEASUREMENT_ID`, `VITE_PLAUSIBLE_DOMAIN`, `VITE_GOOGLE_ADS_ID`, `VITE_GOOGLE_ADS_PURCHASE_LABEL`.

## Project Structure

```
server/
  index.js          # Express server — API routes + static serving
src/
  data/products.js  # Product and collection catalog
  composables/       # useCart, etc.
  pages/            # Routed Vue page components
  components/       # SiteHeader, CartDrawer, ShirtCard, CollectionCard, Icon
  styles.css        # All styles (no component-scoped CSS)
scripts/            # Prerendering, Printify webhook registration
tests/              # Vitest suite
```

## Testing

```bash
npm test
```

Covers checkout validation, order lifecycle, fulfillment status, email builders, cart/design composables, and component rendering. The Postgres-backed integration tests run only with `TEST_DB=1` and `DATABASE_URL` set — CI provides this automatically (see `.github/workflows/test.yml`).

## Deployment

Deployed on Railway: push to GitHub, connect the repo, set the environment variables above, and Railway runs `npm start`. See [`CLAUDE.md`](CLAUDE.md) for the full production setup and go-live checklist (Stripe webhooks, Printify webhook registration, email domain verification).
