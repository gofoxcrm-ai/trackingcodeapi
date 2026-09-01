# Gofox Tracking Code API

Website / app **visitor tracking** for Gofox — page views, events, identify, embeddable tracker, and Tag Manager bootstrap.

> Sibling products: [REST API](https://github.com/gofoxcrm-ai/restapi) · [Webhooks](https://github.com/gofoxcrm-ai/webhooks) · [SSO](https://github.com/gofoxcrm-ai/sso)

**Phased docs:** [PHASES.md](./PHASES.md)

---

## Status overview

| Phase | Area | Status |
|-------|------|--------|
| 1 | Public tracker + ingest endpoints | ✅ Live |
| 2 | Tag Manager + analytics UI APIs | ✅ Live |
| 3 | Site plugins + email pixel URLs | ✅ Live (related) |
| Media | Screenshots / video | 🚧 Placeholders |

---

## Base URL

```
Production: https://api.gofox.io/api/v1/public
Local:      http://localhost:4000/api/v1/public
```

`:orgSlug` is your organization public slug (shown in **Account Settings → API & tracking**).

---

# Phase 1 — Visitor tracker

## Install snippet

```html
<script>
  window.GoFoxAnalytics = { accountId: "YOUR_ORG_SLUG" };
</script>
<script
  async
  src="https://api.gofox.io/api/v1/public/tracking/YOUR_ORG_SLUG/tracker.js"
></script>
```

Alternate entry:

```html
<script
  async
  src="https://api.gofox.io/api/v1/public/analytics.js?account=YOUR_ORG_SLUG"
></script>
```

Browser helpers after load: `GoFox.track`, `GoFox.identify`, `GoFox.page` (exact names as exported by `tracker.js`).

These endpoints are **public** (no API key). Do not put secrets in the snippet.

![Tracker install (placeholder)](docs/assets/tracker-install-snippet.png)

[Install video (placeholder)](docs/assets/tracker-install.mp4)

## Public endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/tracking/:orgSlug/tracker.js` | Browser tracker script |
| `GET` | `/analytics.js?account=:orgSlug` | Alias script entry |
| `POST` | `/tracking/:orgSlug/pageview` | Record a page view |
| `POST` | `/tracking/:orgSlug/event` | Record a custom event |
| `POST` | `/tracking/:orgSlug/track` | Batch track (pageview / event / heartbeat) |
| `POST` | `/tracking/:orgSlug/identify` | Attach visitor ↔ email / contact |

Rate limit: **120 requests / minute** on `/tracking`. CORS is open for browser use. Invalid payloads return `400` (Zod).

### Example — pageview

```bash
curl -s -X POST "$API/tracking/acme/pageview" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://www.example.com/pricing",
    "title": "Pricing",
    "referrer": "https://google.com",
    "visitorId": "vid_…"
  }'
```

### Example — identify

```bash
curl -s -X POST "$API/tracking/acme/identify" \
  -H "Content-Type: application/json" \
  -d '{
    "visitorId": "vid_…",
    "email": "visitor@example.com"
  }'
```

## Lead ingest + visitor link

When ingesting leads via the REST/ingest APIs, pass `visitor_id` / `visitorId` so Gofox can stitch browsing history to the CRM record. See [restapi — Phase 2 ingest](https://github.com/gofoxcrm-ai/restapi).

Implementation: `gofox-server/src/modules/visitor-tracking/`.

---

# Phase 2 — Tag Manager & analytics UI

## Tag Manager (GTM / GA4)

Org settings: **Account Settings → Tag Manager**

| Field | Purpose |
|-------|---------|
| Enabled | Toggle injection |
| GTM container ID | e.g. `GTM-XXXX` |
| GA4 measurement ID | e.g. `G-XXXX` |
| Data layer name | Default `dataLayer` |

Public pages (forms, landing pages, Content Hub / website, site messages) bootstrap these tags via `PublicTagManagerBootstrap` using the org’s public tracking payload (often loaded with site-messages / page config).

> Plan note: Tag Manager is configured in product settings; gating is softer than SSO/API. Visitor analytics APIs use the **`visitor_tracker`** add-on; Marketing → Web Analytics summary uses plan feature **`web_analytics`** (Prime+).

## Tenant analytics APIs (authenticated)

Session JWT + **`visitor_tracker`** add-on:

```
GET /api/v1/tenant/visitor-tracking/analytics
GET /api/v1/tenant/visitor-tracking/visitors
GET /api/v1/tenant/visitor-tracking/visitors/:id
GET /api/v1/tenant/visitor-tracking/live
GET /api/v1/tenant/visitor-tracking/contacts/:id
```

Web analytics summary (plan **`web_analytics`**):

```
GET /api/v1/tenant/web-analytics/summary
```

UI: **Marketing → Web Analytics** (overview, visitors, live).

![Web analytics live (placeholder)](docs/assets/web-analytics-live.png)

---

# Phase 3 — Related surfaces

## Site plugin helpers

For WordPress / Wix-style plugins (API key scopes `integrations:read` or `ingest:write`):

```
GET /api/v1/integrations/site/status
GET /api/v1/integrations/site/forms
GET /api/v1/integrations/site/landing-pages
```

`status` includes org slug and tracker URL template. Full CRM REST docs: [restapi](https://github.com/gofoxcrm-ai/restapi).

## Email open / click tracking (campaign ops)

Not part of the website tracker product, but related public URLs under messaging:

```
GET /api/v1/public/messaging/track/open.gif
GET /api/v1/public/messaging/track/open/:token
GET /api/v1/public/messaging/track/click/:token
```

Base URL override: `EMAIL_TRACKING_BASE_URL` (else `API_BASE_URL`). See [webhooks Phase 3](https://github.com/gofoxcrm-ai/webhooks) for ESP inbound setup.

## Public marketing surfaces that load tracking

| Surface | Typical public API prefix |
|---------|---------------------------|
| Forms | `/api/v1/public/forms/…` |
| Landing pages | `/api/v1/public/landing-pages/…` |
| Content Hub / website | `/api/v1/public/cms/:orgSlug/…` |

---

## Adding endpoints

1. Extend `gofox-server/src/modules/visitor-tracking/`
2. Keep public routes under `/api/v1/public/tracking/...`
3. Prefer additive, backward-compatible payload fields
4. Update this README + [PHASES.md](./PHASES.md) in the same PR

---

## Media placeholders

| File | Purpose |
|------|---------|
| `docs/assets/tracker-install-snippet.png` | Snippet UI |
| `docs/assets/web-analytics-live.png` | Live visitors dashboard |
| `docs/assets/tag-manager-settings.png` | GTM / GA4 settings |
| `docs/assets/tracker-install.mp4` | Install walkthrough |

---

## Related docs

- Monorepo: `docs/SITE_EMBEDS.md`, `docs/DEVELOPER_API.md` (reconcile with this repo if they drift)
- Content / REST: [restapi](https://github.com/gofoxcrm-ai/restapi)

---

## License

Documentation © Gofox. Visitor Tracker may require an add-on entitlement; Web Analytics may require plan feature `web_analytics`.
