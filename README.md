# CHEYENNE — Complete Website Package

## Files
- `index.html` — responsive CHEYENNE storefront
- `admin.html` — dedicated admin entry point (loads the same admin interface in app.js)
- `style.css` — all storefront and admin styles
- `app.js` — storefront, product galleries, cart, checkout, and admin dashboard
- `config.js` — Worker API URL (must be set after backend deployment)
- `assets/` — all 11 original uploaded CHEYENNE brand reference images, plus logo assets
- `worker/` — Cloudflare Worker, D1 schema, Wrangler configuration

## Open locally / on GitHub Pages
Upload all root files and the `assets/` folder into the GitHub repository root. Do not upload just the ZIP. The images use relative paths and work on GitHub Pages project URLs. Open `index.html` for storefront or `admin.html` for admin. You can also use `index.html#/admin`.

## IMPORTANT — Live backend
This is NOT a complete live shop until you deploy and configure Cloudflare Worker, D1, R2 and a supported payment provider. The storefront displays reference products until the real product database is populated. The admin requires the backend; it cannot securely save products using static GitHub Pages alone. Read `worker/` and the deployment instructions below. Do not take live orders before configuring shipping, taxes, security and payment flow.

# CHEYENNE — Storefront + Store Management

## Included
- `public/` — responsive storefront, shopping bag, product detail galleries, secure checkout redirect, and `/\#/admin` management UI.
- `worker/src.js` — Cloudflare Worker API for catalog, authentication, image uploads, inventory, order records and Stripe Checkout.
- `worker/schema.sql` — D1 database tables.
- `worker/wrangler.toml` — deployment configuration.

## Setup
1. Install Node.js and Wrangler (`npm install -g wrangler`) and sign in (`wrangler login`).
2. Create a D1 database: `wrangler d1 create cheyenne-store`. Copy its ID into `worker/wrangler.toml`.
3. Create R2 bucket: `wrangler r2 bucket create cheyenne-product-images` (requires R2 enabled).
4. From `worker/`, run `wrangler d1 execute cheyenne-store --remote --file=schema.sql`.
5. Create a long, unique admin password. Hash it using `node -e "require('crypto').createHash('sha256').update(process.argv[1]).digest('hex')" 'YOUR_PASSWORD'` (the command as written needs `console.log` to print: see below). Better: `node -e "console.log(require('crypto').createHash('sha256').update(process.argv[1]).digest('hex'))" 'YOUR_PASSWORD'`.
6. Set secrets from `worker/`: `wrangler secret put ADMIN_PASSWORD_HASH`, `wrangler secret put ADMIN_SECRET` (random 32+ characters), `wrangler secret put STRIPE_SECRET_KEY`, `wrangler secret put STRIPE_WEBHOOK_SECRET`.
7. Set `ALLOWED_ORIGIN` and `STORE_URL` in `wrangler.toml` to the exact public storefront URL, then `wrangler deploy`.
8. In `public/config.js`, replace the placeholder with your actual deployed Worker URL.
9. Host `public/` on Cloudflare Pages or GitHub Pages. For GitHub Pages, place the **contents** of `public/` at the repository root.
10. In Stripe Dashboard, register the Worker endpoint `https://YOUR-WORKER.workers.dev/api/stripe-webhook` for `checkout.session.completed` and set the signing secret.
11. Open `https://YOUR-SITE/#/admin`, sign in with your password, and create real products, images, size/color variants and stock.

## Important before accepting real orders
- **The storefront is not live until the Worker, D1, R2, domain and Stripe are configured.** No fake orders or card collection are enabled when the payment provider is absent.
- Stripe merchant eligibility depends on the owner's business country; confirm that before selecting Stripe. The checkout assumes USD and currently has **no automatic shipping charge, tax calculation, shipping-zone validation, or customer accounts**. These must be implemented/configured before launch. Set the storefront to private until then.
- The current order system marks payments paid on verified Stripe webhook events. Production launch needs **atomic inventory reservation/stock enforcement under concurrent orders**, automated customer/merchant notifications, refund handling, policies and stronger admin authentication (rate limits, account recovery, MFA). In particular, the current stock decrement is not a full reservation system.
- Do not use the sample images as individually approved product photos or publish sample pricing without merchant approval. The preview catalog is not stored in D1; add approved products in the admin.
- `ALLOWED_ORIGIN` must match the real storefront domain exactly, including `https://`. Admin cookies use SameSite=None; HTTPS is required.
- Customer account is informational only; no login or personal order history has been implemented.

## URL structure
- Storefront: `/#/`
- Shop: `/#/shop`
- Product: `/#/product/<id>`
- Cart: `/#/cart`
- Checkout: `/#/checkout`
- Admin: `/#/admin`


## Dedicated admin page
Open `admin.html` (or `/admin.html` on your hosted domain). This page now has its own login and management layout, separate from the storefront. Set the Worker URL in `config.js`, deploy D1/R2, and configure `ADMIN_SECRET` and `ADMIN_PASSWORD_HASH` in the Worker. There is no default password: set your own secure password. This is not a working login until the Worker is deployed.
