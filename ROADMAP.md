# Gatekeeper — Product Roadmap

## Vision
DNS for human identity. Your reachability is a function you control, not a static value others possess.

## The Core Thesis
AI agents will scatter your real identity data at machine speed — 50 agreements/day vs a human's 5/week. Without a sovereignty layer, the privacy problem becomes catastrophic. Gatekeeper is that layer.

---

## Phase 1: Predatory T&C Index (NOW — March 2026)
**Goal:** Domain authority, waitlist, SEO, build the scoring engine

- [x] Landing page with 30 scored services
- [x] Real T&C analysis with verbatim clauses
- [x] Comparison tool
- [x] Personal exposure calculator
- [ ] Shareable report cards (OG images, social share)
- [ ] Interactive data broker network map
- [ ] Action items per service (opt-out links, alternatives)
- [ ] Hash routing for direct service links
- [ ] "Request a service" form
- [ ] T&C change monitoring (automated scraping + diffing)
- [ ] Category pages for SEO ("/best-messaging-app-for-privacy")
- [ ] Blog/changelog: "This Week in Predatory Terms"

**Success metric:** 10K unique visitors, 1K waitlist signups, 3+ press mentions

---

## Phase 2: API + MCP + CLI (April 2026)
**Goal:** Make T&C intelligence programmable — agents and developers can query it

### 2a. REST API
```
GET /api/v1/score/tiktok
GET /api/v1/scores?category=social&grade=F
GET /api/v1/compare?a=whatsapp&b=signal
GET /api/v1/check-url?url=https://example.com/signup
POST /api/v1/evaluate  (body: raw ToS text → returns score)
```

Response:
```json
{
  "service": "TikTok",
  "grade": "D",
  "score": 26,
  "risks": ["biometric_collection", "ai_training", "location_tracking"],
  "recommendation": "avoid",
  "alternatives": [{"name": "YouTube Shorts", "grade": "D"}, {"name": "Instagram Reels", "grade": "D"}],
  "optOutUrl": "https://...",
  "lastUpdated": "2026-03-25"
}
```

### 2b. MCP Server (Model Context Protocol)
The killer feature. Any AI agent using MCP can:
- **Before signing up:** `gatekeeper.check("spotify.com")` → "Grade C. Uses your listening data for AI training. Recommend opt-out at [url]."
- **Before agreeing to ToS:** `gatekeeper.evaluate(tosText)` → Real-time scoring of arbitrary terms
- **Policy enforcement:** `gatekeeper.policy(userPolicy, service)` → "Conflicts: service requires arbitration, user policy rejects it"
- **Audit trail:** `gatekeeper.log(action, service)` → Log what the agent agreed to on behalf of the user

MCP Tools:
```
gatekeeper_check_service    — Look up a known service by name/domain
gatekeeper_evaluate_terms   — Score arbitrary ToS text
gatekeeper_check_policy     — Compare service terms against user's personal policy
gatekeeper_get_alternatives — Get privacy-respecting alternatives
gatekeeper_log_agreement    — Record that an agent agreed to terms on user's behalf
gatekeeper_audit            — Show all agreements made by agents
```

### 2c. CLI
```bash
$ gatekeeper check tiktok
TikTok — Grade D (26/100)
Worst: Biometric collection, AI training, location tracking
"TikTok collects your biometrics and claims sweeping rights to all content."
Opt out: https://www.tiktok.com/setting/privacy

$ gatekeeper compare whatsapp signal
WhatsApp: C (52/100) vs Signal: A (86/100)
Signal wins on: data_selling, ai_training, cross_platform, data_retention
WhatsApp wins on: (nothing)

$ gatekeeper scan --browser
Scanned 47 services from your browser history.
Exposure score: 73/100 (HIGH)
Worst: Facebook (F), TikTok (D), Google (D)
3 services changed terms since you last agreed.

$ gatekeeper policy set --no-arbitration --no-ai-training --delete-on-request
Policy saved. 18 of your 47 services violate this policy.

$ gatekeeper audit
Last 7 days: Your agent agreed to 23 terms of service.
3 flagged: Violated your --no-ai-training policy.
```

**Success metric:** 500+ MCP installs, 1K+ npm installs, integrated into 3+ agent frameworks

---

## Phase 3: Chrome Extension (May-June 2026)
**Goal:** Consumer product — passive protection for normal humans

Features:
- **Form interception** — Detects signup forms, generates scoped email/phone aliases
- **T&C scanner** — Reads terms before you click "I agree", shows score overlay
- **Conflict detection** — "This service requires arbitration. Your policy rejects arbitration."
- **Change alerts** — "Spotify updated their terms. New: they can now use your data for AI training."
- **Identity dashboard** — See every service, revoke access, track what's shared where
- **Kill switches** — One-click revoke for any connection

**Revenue model:** Free tier (5 services) → Pro $9/mo (unlimited + alerts + aliases)

**Success metric:** 10K installs, $5K MRR

---

## Phase 4: Protocol (Q3-Q4 2026)
**Goal:** Gatekeeper becomes a standard, not just a product

- **Permission Language spec** — Machine-readable format for identity sharing policies
- **Gate middleware** — Open-source proxy that evaluates requests against personal policy
- **Identity Vault** — Encrypted on-device identity store (name, email, phone, addresses)
- **Ledger** — Tamper-evident log of all identity sharing events
- **Graph** — Full map of where your data lives, updated in real-time

The protocol emerges from usage. Extension proves what people need → protocol formalizes it → regulation + AI agent adoption drives standardization.

---

## Architecture

```
User / Agent
    ↓
[Permission Language] — "I allow email sharing with Grade B+ services only"
    ↓
[Gate Middleware] — Evaluates request against policy
    ↓
[Identity Vault] — Encrypted store (on-device)
    ↓
[Ledger] — Tamper-evident log of all sharing events
    ↓
[Graph] — Full data map over time
```

---

## Competitive Landscape
| Competitor | What they do | What they miss |
|---|---|---|
| SimpleLogin (Proton) | Email aliases | No T&C intelligence, no policy engine, no audit |
| Firefox Relay | Email/phone masking | No scoring, no graph, no agent integration |
| Apple Hide My Email | iCloud email aliases | Apple-only, no cross-platform, no terms analysis |
| ToS;DR | Crowdsourced ToS ratings | No API, no MCP, no agent integration, stale data |
| Privacy.com | Virtual credit cards | Financial only, no identity layer |

**Our moat:** Only solution that combines T&C intelligence + programmatic API + agent integration + identity graph + revocation. Everyone else does one piece.

---

## Revenue Projections
- Phase 2 API: Free tier + $29/mo developer tier → $2K/mo by month 3
- Phase 3 Extension: $9/mo Pro → $5K/mo by month 3
- Phase 4 Enterprise: B2B licensing to agent platforms → $20K+/mo
- Total target: $30K/mo within 12 months

---

## Tech Stack
- **Website:** Static HTML/JS (GitHub Pages) → migrate to Cloudflare Pages when needed
- **API:** Cloudflare Workers (edge, cheap, fast)
- **MCP Server:** TypeScript, npm package
- **CLI:** Node.js, npm package
- **Extension:** Chrome Manifest V3, vanilla JS
- **T&C Monitoring:** Cron worker that scrapes + diffs ToS pages weekly
- **Database:** Cloudflare D1 (SQLite at the edge) or Supabase

## Next Actions
1. Finish Phase 1 (data broker map, share buttons, actions)
2. Build MCP server + CLI as npm packages
3. Set up T&C change monitoring pipeline
4. Launch on HN: "We scored the terms of service for 30 services"
