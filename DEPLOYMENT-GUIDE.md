# CHEYENNE — Storefront + Admin + Cloudflare Worker (Updated)

## Which files to replace in GitHub
Upload the entire contents of this ZIP's `cheyenne-main` folder to the root of your existing `pixel-built/cheyenne` repository, replacing existing files.
This is a MULTI-FILE website. Do not replace only index.html.

- index.html: storefront HTML shell (unchanged)
- app.js: storefront, bag repair, carousel, contact, checkout
- style.css: storefront styling
- admin.html: admin HTML shell (unchanged)
- admin.js: grouped inventory interface
- admin.css: grouped inventory styling
- worker/src.js: backend; preserves product variant IDs on edits, adds order status lookup
- config.js: existing deployed API address retained
- assets/logo.png: user-provided CHEYENNE gold logo

## Replace these image files (keep exact filenames)
- assets/hero-desktop.jpg — main desktop banner, recommended 1920x1000 or wider
- assets/hero-mobile.jpg — mobile banner, recommended 900x1200
- assets/collection-new.jpg — New Arrivals
- assets/collection-main.jpg — Collections
- assets/collection-accessories.jpg — Accessories
- assets/contact-banner.jpg — contact page background

Starter images are included so nothing is blank.

## Cloudflare Worker deployment
Your GitHub Pages deployment DOES NOT deploy the Worker automatically.
After updating worker/src.js in GitHub, also deploy that updated Worker to `cheyenne-store-api` in Cloudflare.
Keep D1 binding `DB` and R2 binding `BUCKET` (the updated Worker accepts either `BUCKET` or `IMAGES`).
No need to rename your existing R2 binding.

The backend still requires:
- ADMIN_SECRET and ADMIN_PASSWORD_HASH (keep your existing working admin credentials)
- ALLOWED_ORIGIN = https://pixel-built.github.io
- STORE_URL = https://pixel-built.github.io/cheyenne
- STRIPE_SECRET_KEY = your Stripe secret key (test key for test mode)
- STRIPE_WEBHOOK_SECRET = signing secret from Stripe webhook endpoint

Stripe webhook URL:
https://cheyenne-store-api.raspy-resonance-4920.workers.dev/api/stripe-webhook
Subscribe to `checkout.session.completed` in Stripe.
Do NOT put Stripe secret keys in config.js, GitHub, or frontend HTML.

## Testing
1. Save an existing product again, then verify variant IDs remain unchanged.
2. Remove any previously invalid cart items if they cannot be matched to size/colour.
3. Check D1 inventory is sufficient for checkout.
4. Use Stripe test keys and Stripe test cards for testing, NOT live cards.
5. Confirm a paid test order is recorded and marked paid by the webhook.
6. Check mobile navigation, carousel, product detail, contact email draft, admin login.

## Important limitations
- Checkout redirects to Stripe only after valid stock and Stripe configuration. We cannot activate Stripe for you.
- Contact form opens the customer's email client; it is not a server-side email sending service.
- Email/WhatsApp order notifications are NOT enabled in this ZIP. A verified email provider and/or WhatsApp Business API must be configured before automatic notifications can be delivered.
- No customer account registration is included.
- No shipping price engine is configured. Set up shipping and tax rules before selling internationally.
- The order-success page is not proof of payment. The Stripe webhook is the authoritative payment record.
- Facebook/TikTok links were omitted until real CHEYENNE profile URLs are provided.
