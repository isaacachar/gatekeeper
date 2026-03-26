# Gatekeeper — Roadmap

**Last updated:** 2026-03-25
**Status:** Phase 1 in progress

---

## Current Status

**Phase 1 is ~60% done.**

What's shipped:
- ✅ Landing page with 30+ scored services and real T&C analysis
- ✅ Comparison tool
- ✅ Personal exposure calculator
- ✅ Data broker network map (map.html)
- ✅ T&C data JSON (tc-data.json, 90KB+ of scored services)
- ✅ Data broker dataset (data-brokers.json)
- ✅ GitHub repo + CI setup

What's remaining (Phase 1):
- ❌ Shareable report cards (OG images, social share)
- ❌ Action items per service (opt-out links, alternatives)
- ❌ Hash routing for direct service links
- ❌ "Request a service" form
- ❌ T&C change monitoring pipeline
- ❌ Category pages for SEO
- ❌ Blog/changelog
- ❌ HN launch

**Blocking question:** When to launch on HN — before or after all Phase 1 items? Recommend launching with what we have + share/direct-link features. The rest can ship in the week after launch.

---

## Phase 1: T&C Index Website (March 2026)

**Objective:** Establish domain authority, build waitlist, prove the data is valuable.

### Deliverables

| Item | Status | Est. Effort | Notes |
|---|---|---|---|
| Landing page with scored services | ✅ Done | — | 30+ services scored |
| Real T&C analysis with verbatim clauses | ✅ Done | — | |
| Comparison tool | ✅ Done | — | |
| Personal exposure calculator | ✅ Done | — | |
| Data broker network map | ✅ Done | — | map.html |
| Shareable report cards | ❌ TODO | 1 day | OG meta tags + share buttons + per-service URL |
| Hash routing for direct links | ❌ TODO | 0.5 day | #service=spotify → scroll to + highlight |
| Action items per service | ❌ TODO | 1 day | Opt-out links, deletion guides, alternatives |
| "Request a service" form | ❌ TODO | 0.5 day | Simple form → GitHub issue or email |
| T&C change monitoring | ❌ TODO | 2-3 days | Cron worker: scrape ToS pages, diff, alert |
| Category/SEO pages | ❌ TODO | 2 days | /best-messaging-for-privacy, etc. |
| Blog: "This Week in Predatory Terms" | ❌ TODO | Ongoing | Weekly post on T&C changes |
| HN launch | ❌ TODO | 1 day | Post + be available to respond for 24h |

### Success Metrics
- 10K unique visitors in first month
- 1K waitlist signups
- 3+ press/blog mentions
- Top 5 on HN for at least 2 hours

### Dependencies
- None — self-contained static site

### Estimated Total Remaining Effort
~7-8 days of work to complete Phase 1 and launch.

---

## Phase 2: API + MCP Server + CLI (April 2026)

**Objective:** Make T&C intelligence programmatic. Developers and AI agents can query scores, evaluate terms, enforce policies.

### Deliverables

| Item | Est. Effort | Dependencies | Notes |
|---|---|---|---|
| **REST API on Cloudflare Workers** | 3-4 days | D1 database setup | See PRODUCT-SPEC.md for endpoints |
| → GET /scores/{service} | Included | | Public, rate-limited |
| → GET /scores (search/filter) | Included | | |
| → GET /compare | Included | | |
| → POST /evaluate | Included | | Authenticated, scores raw ToS text |
| → GET /alternatives/{service} | Included | | |
| → GET /changes | Included | | T&C change feed |
| **Database migration** | 1 day | | Move tc-data.json → D1 tables |
| **API key system** | 1-2 days | | Key generation, tier enforcement, usage tracking |
| **MCP Server (npm package)** | 3-4 days | API must be live | See PRODUCT-SPEC.md for tool specs |
| → gatekeeper_check_service | Included | | |
| → gatekeeper_evaluate_terms | Included | | |
| → gatekeeper_check_policy | Included | | |
| → gatekeeper_get_alternatives | Included | | |
| → gatekeeper_log_agreement | Included | | Local audit log |
| → gatekeeper_audit | Included | | |
| **CLI (npm package)** | 2-3 days | API must be live | `gatekeeper check`, `compare`, `scan`, `policy` |
| **T&C scoring engine v2** | 3-4 days | | Hybrid rules + LLM for new services |
| **API documentation** | 1 day | | OpenAPI spec + examples page |
| **Stripe integration** | 1-2 days | | Developer tier billing ($29/mo) |
| **Launch: MCP ecosystem** | 1 day | | Submit to MCP directories, post on HN |

### Success Metrics
- 500+ MCP server installs (npm)
- 1K+ CLI installs
- 50+ paying developer tier users ($1,450/mo)
- Integration into 3+ agent frameworks/tutorials

### Dependencies
- Phase 1 must be launched (credibility, waitlist, data)
- D1 database provisioned
- Stripe account set up
- npm org claimed (`@gatekeeper`)

### Estimated Total Effort
~15-18 days of focused work. Can be compressed with a contractor on the CLI while we build the API.

---

## Phase 3: Chrome Extension (May-June 2026)

**Objective:** Consumer product. Passive privacy protection for people who don't use CLIs.

### Deliverables

| Item | Est. Effort | Dependencies | Notes |
|---|---|---|---|
| **Extension scaffold (MV3)** | 1 day | | Manifest, service worker, content script, popup |
| **Form interception** | 3-4 days | | Detect signup forms, offer alias substitution |
| **Email alias generation** | 2-3 days | Relay decision | SimpleLogin API integration or custom relay |
| **T&C score overlay** | 2-3 days | API live | Badge on ToS pages + "I agree" checkboxes |
| **Popup UI** | 2 days | | Current site score, quick actions, policy status |
| **Dashboard page** | 3-4 days | | Full service list, graph, audit log, kill switches |
| **Policy engine (client-side)** | 2-3 days | | Evaluate requests against user-defined policy |
| **Identity vault** | 2-3 days | | Encrypted local storage with passphrase |
| **Audit log** | 1-2 days | | Append-only log with hash chain |
| **Change alerts** | 1-2 days | T&C monitoring | "Spotify changed their terms" notifications |
| **Settings + onboarding** | 1-2 days | | First-run flow, import from website scan |
| **Chrome Web Store submission** | 1 day | | Review process takes 1-3 days |
| **Free/Pro tier enforcement** | 1-2 days | Stripe | Server validates Pro status for premium features |

### Success Metrics
- 10K installs in first 3 months
- $5K MRR from Pro subscriptions ($9/mo × 556 users)
- 4.5+ star rating on Chrome Web Store
- < 2% uninstall rate in first week

### Dependencies
- API must be stable and documented (Phase 2)
- Email relay decision made (build vs. SimpleLogin vs. skip)
- Chrome Web Store developer account ($5 one-time)
- Privacy policy and terms of service (yes, we need our own — the irony)

### Estimated Total Effort
~22-28 days. This is the biggest phase. Consider hiring a contractor for the dashboard UI.

### Risks
- Chrome Web Store review can be unpredictable with MV3
- Form detection heuristics will need constant tuning (sites change their markup)
- Email deliverability if we build own relay (recommend against for v1)

---

## Phase 4: Protocol Standardization (Q3-Q4 2026)

**Objective:** Gatekeeper becomes a standard, not just a product. Open protocol that anyone can implement.

### Deliverables

| Item | Est. Effort | Dependencies | Notes |
|---|---|---|---|
| **Permission Language spec** | 2-3 weeks | Extension battle-tested | JSON schema for identity policies and grants |
| **Gate middleware (open source)** | 2-3 weeks | Spec finalized | Reference implementation of policy evaluation |
| **Identity Vault spec** | 1-2 weeks | | Encryption, storage, sync protocol |
| **Ledger spec** | 1-2 weeks | | Hash chain format, sync protocol |
| **Graph data model** | 1-2 weeks | | Service-to-broker-to-buyer relationships |
| **Reference implementations** | 2-3 weeks | Specs done | TypeScript + Python libraries |
| **Standards body engagement** | Ongoing | | W3C, IETF, or independent spec org |
| **B2B licensing program** | 1-2 weeks | | Agent platforms pay to integrate |
| **Enterprise dashboard** | 3-4 weeks | | Multi-user, compliance reporting, SSO |

### Success Metrics
- Protocol adopted by 2+ agent platforms
- 3+ independent implementations
- $20K+/mo enterprise revenue
- W3C or IETF interest/working group

### Dependencies
- Extension must be live with real users generating real data
- Legal counsel for licensing terms
- Community building (contributors, implementers)

### Estimated Total Effort
This is a quarter-long effort minimum. Not solo work — needs community and possibly a small team.

### Risks
- Protocol design by one person tends to miss edge cases — need external feedback early
- Standards process is slow (years, not months) — focus on de facto adoption first
- Enterprise sales cycle is long — don't count on this revenue early

---

## Timeline Summary

```
2026
Mar         Apr              May         Jun         Jul-Sep     Oct-Dec
|-----------|----------------|-----------|-----------|-----------|----------|
 Phase 1     Phase 2          Phase 3                 Phase 4
 T&C Index   API+MCP+CLI      Extension               Protocol
 HN Launch   MCP Launch       CWS Launch              Enterprise
             $29/mo dev tier  $9/mo Pro               $20K+/mo B2B
```

---

## Resource Assumptions

- **Phase 1-2:** Solo (Isaac) + AI assistance. Contractor possible for CLI/docs.
- **Phase 3:** Solo + contractor for dashboard UI. Extension core done by Isaac.
- **Phase 4:** Needs community. Can't standardize alone. Budget for 1-2 part-time contributors.

---

## Decision Log

| Date | Decision | Rationale |
|---|---|---|
| 2026-03-25 | Created structured roadmap | Replacing freeform doc with trackable phases |
| | Database: D1 (pending) | Cloudflare-native, cheap, edge SQLite |
| | Email relay: SimpleLogin API for v1 (pending) | Don't build email infra from scratch |
| | Phone aliases: skip in v1 (pending) | Focus on email first, phone is expensive |
| | T&C scoring: hybrid rules+LLM (pending) | Rules for known patterns, LLM for novel |

*Update this log when decisions are made. "Pending" means proposed but not committed.*
