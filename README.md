# SYNTH_OS v4

This HTML "idle clicker" game is built to run in a static web environment and support
advertising (Google AdSense) and analytics (Google Analytics 4). The built-in
`watchAd()` function currently simulates a rewarded video; replace with a real SDK call for production.

## Getting live AdSense running

1. **Create an AdSense account** and add your website/domain. Wait for approval.
2. In the AdSense dashboard, create ad units to obtain **slot IDs**.
3. Replace placeholders in `synth-os-v4.html`:
   * `data-ad-client="ca-pub-XXXXXXXXXXXXXXXX"` → `ca-pub-YOURPUBID` (your publisher ID)
   * `data-ad-slot="1234567890"` and others → your real slot IDs
   * Remove `data-adtest="on"` attributes once you're ready to serve real ads.
4. If you serve locally for development, run a simple HTTP server instead of
   opening the file directly (AdSense will not load over `file://`):

```powershell
python -m http.server 8000
# then browse to http://localhost:8000/synth-os-v4.html
```

5. Disable any adblockers. Accept the cookie-consent banner when it appears;
   the AdSense script is added dynamically only after consent.
6. Check DevTools Network tab for `adsbygoogle.js` and for ad slot requests.

## Analytics (GA4)

1. Create a Google Analytics 4 property and copy the measurement ID (`G-XXXXXX`).
2. Replace the placeholder in the `loadGA4()` call inside `synth-os-v4.html`.
3. Events logged via the `A.track(...)` wrapper will now appear under your GA4 run
   dashboard. Use these to measure retention, ad clicks, rewarded ad completions,
   and in‑game purchases.

## Rewarded video ads

The `watchAd()` function currently displays a fake 6‑second countdown and then
calls `onAdComplete()` to grant the boost. To use a real rewarded ad SDK, replace
that stub with code from your chosen network (AdMob, IronSource, etc.) and be sure
`onAdComplete()` is called only when the user has actually earned the reward.

## Monetization with Firestore

If you plan to accept real money, you'll typically:

1. Use a payment processor (Stripe, PayPal) to collect funds.
2. Verify the payment in a server or Firebase Cloud Function.
3. Update a Firestore document for the player to credit tokens.
4. In the game, read that document and grant the purchased items.
5. Log purchase events via `A.track('purchase', {amount, item});` for analytics.

Firestore itself doesn’t "earn" revenue; it simply stores the state of purchases.

### Privacy & consent

* The site uses `localStorage` to track state and a flag (`synth_consent`) for
  cookie consent. Nothing personal is collected by default.
* Ads and analytics scripts load only after the user accepts the cookie banner.
* A bare-bones `privacy.html` page is included; you should expand it with a full
  policy before going live.

## Deployment

1. Host the files on a real domain (GitHub Pages, Netlify, any static host).
   * **Netlify tip:** upload the entire repo or connect a GitHub repo. Netlify
     serves `index.html` by default, so either rename your game file to
     `index.html` or include a redirect stub (this repo already contains one).
     After deployment your site will be available at something like
     `https://<name>.netlify.app/`.
2. Point that domain in AdSense/analytics settings and wait for verification.
3. Monitor your GA4 property and AdSense dashboard for impressions, clicks, and
   revenue.
4. Iterate on UX, ad placement, and monetization features.

---

Feel free to ask for help wiring up a specific ad SDK or payment flow!