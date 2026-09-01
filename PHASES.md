# Tracking Code API — documentation & delivery phases

| Phase | Theme | Status |
|-------|--------|--------|
| **1** | Install snippet, tracker.js, pageview/event/identify | ✅ Implemented — [README.md](./README.md#phase-1--visitor-tracker) |
| **2** | Tag Manager (GTM/GA4), tenant analytics add-on, web analytics plan | ✅ Implemented — [README.md](./README.md#phase-2--tag-manager--analytics-ui) |
| **3** | Site plugin helpers; email open/click (campaign ops) | ✅ Paths live — [README.md](./README.md#phase-3--related-surfaces) |

## Phase checklist

### Phase 1
- [x] `tracker.js` + `analytics.js` alias
- [x] `pageview` / `event` / `track` / `identify` POSTs
- [x] Public CORS; rate limit 120/min on `/tracking`
- [x] Snippet in Account Settings → API & tracking
- [ ] Install screenshots / video

### Phase 2
- [x] Org Tag Manager settings (GTM container / GA4)
- [x] Bootstrap on public forms, landing pages, CMS, site messages
- [x] Tenant visitor-tracking APIs (`visitor_tracker` add-on)
- [x] Web analytics summary (`web_analytics` plan feature)

### Phase 3
- [x] Integrations site status (tracker URL template)
- [x] Messaging email open/click track URLs
- [ ] WordPress / Wix plugin deep-dive guides

## Source of truth

| Concern | Path |
|---------|------|
| Public tracking routes | `gofox-server/src/modules/visitor-tracking/` |
| Tag manager serialize | `gofox-server/src/lib/public-tracking.ts` |
| Client bootstrap | `gofox-client/src/components/public/PublicTagManagerBootstrap.tsx` |
| Snippet UI | `gofox-client/.../apiTrackingSettingsLive.tsx` |
