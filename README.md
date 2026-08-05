# Gofox Tracking Code API

Website / app **visitor tracking** for Gofox — page views, events, identify, and the embeddable tracker script. Comparable to EngageBay’s [Tracking Code API](https://www.engagebay.com/api).

> Sibling products: [REST API](https://github.com/gofoxcrm-ai/restapi) · [Webhooks](https://github.com/gofoxcrm-ai/webhooks) · [SSO](https://github.com/gofoxcrm-ai/sso)

---

## Status

| Area | Status |
|------|--------|
| Public tracker.js / analytics.js | ✅ Live |
| Pageview / event / track / identify | ✅ Live |
| Tenant analytics UI | ✅ Live (Visitor Tracker add-on) |
| Screenshots / install video | 🚧 Placeholders below |

---

## Base URL

```
Production: https://api.gofox.io/api/v1/public
Local:      http://localhost:4000/api/v1/public
```

`:orgSlug` is your organization public slug (shown in **Account Settings → API & tracking**).

---

## Install snippet

```html
<script
  async
  src="https://api.gofox.io/api/v1/public/tracking/YOUR_ORG_SLUG/tracker.js"
></script>
```

Alternate:

```html
<script
  async
  src="https://api.gofox.io/api/v1/public/analytics.js?account=YOUR_ORG_SLUG"
></script>
```

<!-- SCREENSHOT: docs/assets/tracker-install-snippet.png
     Placeholder — Account Settings → API & tracking showing the copy-paste snippet.
-->

![Tracker install (placeholder)](docs/assets/tracker-install-snippet.png)

<!-- VIDEO: docs/assets/tracker-install.mp4
     Placeholder — install on a test page, then show Live visitors in Gofox.
-->

[Install video (placeholder)](docs/assets/tracker-install.mp4)

---

## Public endpoints (working)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/tracking/:orgSlug/tracker.js` | Browser tracker script |
| `GET` | `/analytics.js?account=:orgSlug` | Alias script entry |
| `POST` | `/tracking/:orgSlug/pageview` | Record a page view |
| `POST` | `/tracking/:orgSlug/event` | Record a custom event |
| `POST` | `/tracking/:orgSlug/track` | Batch track payload |
| `POST` | `/tracking/:orgSlug/identify` | Attach visitor ↔ contact/email |

These endpoints are **public** (no API key). They are rate-limited and scoped to the org slug. Do not put secrets in the snippet.

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

Exact field schemas are enforced server-side (Zod). Invalid payloads return `400`.

---

## Tenant analytics (authenticated)

Requires plan / add-on **`visitor_tracker`** and a session JWT:

```
GET /api/v1/tenant/visitor-tracking/analytics
GET /api/v1/tenant/visitor-tracking/visitors
GET /api/v1/tenant/visitor-tracking/visitors/:id
GET /api/v1/tenant/visitor-tracking/live
```

UI: Marketing → Web Analytics (overview, visitors, live).

---

## Lead ingest + visitor link

When ingesting leads, pass `visitor_id` so Gofox can stitch browsing history to the CRM contact. See Account Settings → API & tracking notes, and `POST /api/v1/public/leads/ingest`.

---

## Adding endpoints

1. Extend `gofox-server/src/modules/visitor-tracking/`
2. Keep public routes under `/api/v1/public/tracking/...`
3. Update this README with the new method/path
4. Prefer additive, backward-compatible payload fields

---

## Media placeholders

| File | Purpose |
|------|---------|
| `docs/assets/tracker-install-snippet.png` | Snippet UI |
| `docs/assets/web-analytics-live.png` | Live visitors dashboard |
| `docs/assets/tracker-install.mp4` | Install walkthrough |

---

## Related docs

- Monorepo: `docs/SITE_EMBEDS.md`, `docs/DEVELOPER_API.md`
- WordPress / Wix plugins: site embeds package in Gofox

---

## License

Documentation © Gofox. Visitor Tracker may require an add-on entitlement.
