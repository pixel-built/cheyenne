CHEYENNE TEST CHECKOUT - NOT A REAL PAYMENT GATEWAY

GitHub: replace app.js, style.css, admin.js, admin.css.
Cloudflare Worker: back up current Worker, replace code with worker.js, deploy.
Cloudflare Worker > Settings > Variables: add ENABLE_TEST_CHECKOUT as a TEXT variable with value true.
Keep existing DB, BUCKET, ADMIN_PASSWORD, ADMIN_SECRET and ALLOWED_ORIGIN.

Flow: customer fills shipping details -> selects card/PayPal/Apple Pay display method -> simulated payment page -> choose test success/failure. No card information is collected.

Test success marks order test_paid and deducts D1 variant inventory once. Failure marks failed and does not deduct stock. Neither is a real payment. The dashboard must not count test_paid as real paid revenue.

DO NOT USE THIS AS A LIVE PAYMENT PROCESSOR. Disable ENABLE_TEST_CHECKOUT or replace with Stripe/PayPal integration before public sales.

Test admin login, product save, order detail, failed and successful checkout, inventory decrement, and refresh of storefront. Live integration was not tested.
