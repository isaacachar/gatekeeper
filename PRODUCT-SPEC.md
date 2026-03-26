# Gatekeeper — Internal Product Specification

**Last updated:** 2026-03-25
**Author:** Internal
**Status:** Living document — update as decisions are made

---

## Mission

Gatekeeper makes your identity a function you control — not a static value others possess. It's DNS for human identity: scoped, revocable, auditable permission tokens replace the broken model of handing out real contact info and blindly clicking "I agree."

---

## Problem Definition

### What's broken

1. **Identity sharing is one-way and permanent.** You give a service your email, phone, name, address — they own it forever. There's no "unshare" button.
2. **Terms of service are unreadable by design.** Average ToS is 7,000+ words. Nobody reads them. Companies exploit this asymmetry.
3. **No audit trail.** You have no record of what you agreed to, when, or what changed since.
4. **Revocation is fake.** "Delete my account" rarely means "delete my data." GDPR helps in the EU; everywhere else, you're fucked.
5. **Data brokers aggregate what you can't see.** Even if you're careful with Service A, they sell to Broker X who sells to Service B.

### Why now

**AI agents are about to make this 100x worse.** An agent acting on your behalf might sign up for 50 services/day, agree to terms you'd never accept, scatter your real identity data across the internet at machine speed. There is no policy layer between your agent and the world. Gatekeeper is that layer.

Secondary catalysts:
- EU eIDAS 2.0 mandates digital identity wallets by 2026-2027
- AI-generated deepfake impersonation makes identity verification critical
- Post-breach fatigue — consumers are numb but regulators aren't
- Agent frameworks (Claude MCP, OpenAI plugins) need trust infrastructure

---

## Core Concepts

### Identity Vault (Layer 0)
Encrypted, local-first storage of your real identity data. Never leaves your device unencrypted. Contains:
- Legal name, aliases
- Email addresses (real + generated)
- Phone numbers (real + generated)
- Physical addresses
- Payment methods (references only, not full card data)
- Government IDs (hashed references)

### Permission Tokens (Layer 1)
Machine-readable grants for identity sharing. A token says: "Service X can use email alias Y until date Z for purpose P." Tokens are:
- **Scoped** — what data, for what purpose
- **Time-bound** — expiration date or "until revoked"
- **Revocable** — kill switch at any time
- **Auditable** — every use is logged

### The Gate (Layer 2)
Middleware that sits between you (or your agent) and the world. Evaluates every identity request against your personal policy. If a service wants your email and your policy says "only Grade B+ services," the Gate blocks it or generates a scoped alias.

### Audit Ledger (Layer 3)
Tamper-evident, append-only log of every identity sharing event. Who accessed what, when, under what terms, and whether those terms have since changed. Think git log for your identity.

### Identity Graph (Layer 4)
Full map of where your data lives. Shows the network: Service A shares with Broker X who feeds Service B. Over time, this becomes the most valuable dataset — both for individual users and for B2B compliance.

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    USER / AI AGENT                   │
│              (browser, CLI, MCP client)              │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│              LAYER 1: PERMISSION LANGUAGE            │
│                                                     │
│  Policy:  "no-arbitration, no-ai-training,          │
│            max-retention-90d, grade-B-minimum"       │
│                                                     │
│  Request: "spotify.com wants: email, name, dob"     │
│  Grant:   "email=alias@gk.id, name=✓, dob=✗"       │
└──────────────────────┬──────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────┐
│              LAYER 2: THE GATE                       │
│                                                     │
│  ┌──────────┐   ┌──────────┐   ┌──────────────┐    │
│  │ Policy   │──▶│ Evaluate │──▶│ Allow/Deny/  │    │
│  │ Engine   │   │ Request  │   │ Alias        │    │
│  └──────────┘   └──────────┘   └──────────────┘    │
│                                                     │
│  Inputs: T&C score, user policy, service reputation │
└──────────┬──────────────────────────┬───────────────┘
           │                          │
           ▼                          ▼
┌─────────────────────┐  ┌───────────────────────────┐
│  LAYER 0: VAULT     │  │  LAYER 3: AUDIT LEDGER    │
│                     │  │                           │
│  Encrypted local    │  │  Append-only log          │
│  identity store     │  │  Every share event        │
│  (IndexedDB +       │  │  Every revocation         │
│   AES-256-GCM)      │  │  Every T&C change         │
│                     │  │  (local + optional sync)  │
└─────────────────────┘  └───────────────────────────┘
                                    │
                                    ▼
                         ┌───────────────────────────┐
                         │  LAYER 4: GRAPH            │
                         │                           │
                         │  Service → Broker → Buyer  │
                         │  Your data flow map        │
                         │  Aggregate network intel   │
                         └───────────────────────────┘

External Services:
┌─────────────────────────────────────────────────────┐
│  T&C INTELLIGENCE ENGINE (Cloudflare Workers)        │
│                                                     │
│  - Scrapes ToS pages weekly                          │
│  - Diffs changes, scores clauses                     │
│  - Feeds REST API + MCP server                       │
│  - Stores in D1/Supabase                             │
└─────────────────────────────────────────────────────┘
```

---

## Technical Design Decisions

| Decision | Choice | Why |
|---|---|---|
| Vault storage | IndexedDB + AES-256-GCM (browser), encrypted JSON file (CLI) | Local-first, no server dependency, browser-native |
| Permission language | JSON schema (custom, not XACML) | XACML is overengineered XML hell. JSON is what agents speak. |
| Gate location | Client-side (extension/CLI) with server fallback | Zero-trust: policy enforcement happens locally |
| Audit ledger | Local append-only log + optional encrypted sync | Users own their log. Sync is opt-in for multi-device. |
| T&C scoring | Server-side (Workers) | Needs web scraping + ML, can't run in extension |
| Database | Cloudflare D1 | SQLite at the edge. Cheap, fast, no connection pooling BS. |
| API runtime | Cloudflare Workers | Edge compute, $0 at low scale, scales to millions |
| Extension framework | Vanilla JS, Manifest V3 | No React bloat in a browser extension. MV3 is required by Chrome. |
| MCP transport | stdio (local) + SSE (remote) | stdio for local agent integration, SSE for hosted agents |

---

## Data Model

### Identity Record
```json
{
  "id": "uuid-v4",
  "type": "email | phone | name | address | dob | custom",
  "value": "isaac@realemail.com",
  "encrypted": true,
  "created": "2026-03-25T00:00:00Z",
  "aliases": [
    {
      "alias": "gk_8f3a@relay.gatekeeper.id",
      "service": "spotify.com",
      "created": "2026-03-25T00:00:00Z",
      "expires": "2027-03-25T00:00:00Z",
      "active": true
    }
  ]
}
```

### Permission Token
```json
{
  "id": "tok_abc123",
  "service": "spotify.com",
  "serviceGrade": "C",
  "grantedFields": ["email_alias", "name"],
  "deniedFields": ["dob", "phone", "address"],
  "policy": "pol_user_default",
  "conditions": {
    "maxRetentionDays": 90,
    "allowThirdPartySharing": false,
    "allowAiTraining": false
  },
  "granted": "2026-03-25T00:00:00Z",
  "expires": "2027-03-25T00:00:00Z",
  "revokedAt": null,
  "grantedBy": "user | agent:claude-mcp",
  "auditRef": "log_xyz789"
}
```

### User Policy
```json
{
  "id": "pol_user_default",
  "name": "My Default Policy",
  "rules": [
    { "field": "email", "action": "alias_always" },
    { "field": "phone", "action": "alias_if_available", "fallback": "deny" },
    { "field": "name", "action": "allow" },
    { "field": "address", "action": "deny" },
    { "field": "dob", "action": "deny" }
  ],
  "serviceRequirements": {
    "minGrade": "C",
    "blockedClauses": ["forced_arbitration", "ai_training_blanket"],
    "maxRetentionDays": 365,
    "requireDeletionRight": true
  },
  "agentRules": {
    "allowAutoAgree": false,
    "requireConfirmation": "grade_below_B",
    "maxAgreementsPerDay": 10
  }
}
```

### T&C Score Record (server-side)
```json
{
  "service": "spotify.com",
  "domain": "spotify.com",
  "displayName": "Spotify",
  "category": "music_streaming",
  "score": 52,
  "grade": "C",
  "clauses": [
    {
      "category": "ai_training",
      "severity": "high",
      "summary": "Claims right to use listening data and playlists for AI model training",
      "verbatim": "We may use your User Content to develop...",
      "sourceUrl": "https://spotify.com/legal/terms",
      "sourceSection": "Section 6.2"
    }
  ],
  "risks": ["ai_training", "cross_platform_sharing", "data_retention_indefinite"],
  "alternatives": ["apple_music", "tidal"],
  "optOutUrl": "https://spotify.com/account/privacy",
  "lastScraped": "2026-03-20T00:00:00Z",
  "lastChanged": "2026-02-15T00:00:00Z",
  "changeHistory": [
    {
      "date": "2026-02-15",
      "diff": "Added AI training clause to Section 6.2",
      "scoreDelta": -8
    }
  ]
}
```

---

## Security Model

### Threat Model

| Threat | Mitigation |
|---|---|
| Server compromise → user identity leak | Vault is local-only. Server never sees real identity data. |
| Extension compromise (malicious update) | Content Security Policy, no remote code execution, code review before publish |
| Man-in-the-middle on alias routing | All alias routing over TLS. Aliases are random, not derivable from real data. |
| Malicious service requesting excessive data | Gate enforces policy. User must explicitly override denials. |
| Agent over-shares without user consent | Agent rules in policy: confirmation thresholds, daily limits, grade floors |
| Audit log tampering | Append-only with hash chaining (each entry hashes the previous). Optional Merkle tree. |
| T&C scoring manipulation | Scoring engine is server-side, auditable. Raw ToS text + diffs stored for verification. |

### Encryption

- **Vault at rest:** AES-256-GCM with key derived from user passphrase (PBKDF2, 600K iterations)
- **Vault in transit:** Never. Real data doesn't leave the device.
- **Alias email routing:** TLS to relay server. Relay sees alias ↔ real mapping (this is the trust boundary — see open questions).
- **Audit log:** HMAC-SHA256 chain. Each entry includes hash of previous entry.
- **Sync (optional):** End-to-end encrypted with user's key. Server sees only ciphertext.

### Zero-Knowledge Considerations

The ideal: server never knows your real identity. Reality:
- T&C scoring is fully zero-knowledge — server doesn't need to know who's asking
- Email alias relay is NOT zero-knowledge — relay must know the mapping to forward mail
- Options for ZK relay: partner with Proton/SimpleLogin (they already do this), or build our own relay (operational burden)

**Decision needed:** Build relay vs. partner vs. use existing services (SimpleLogin API). See Open Questions.

---

## Extension Architecture

```
┌─────────────────────────────────────────┐
│           CHROME EXTENSION (MV3)         │
│                                         │
│  ┌─────────────┐  ┌─────────────────┐   │
│  │ Content      │  │ Background       │   │
│  │ Scripts      │  │ Service Worker   │   │
│  │             │  │                 │   │
│  │ - Form      │  │ - Policy engine │   │
│  │   detection │  │ - Vault CRUD    │   │
│  │ - T&C page  │  │ - Alias mgmt   │   │
│  │   detection │  │ - API calls     │   │
│  │ - Overlay   │  │ - Alarm/cron    │   │
│  │   injection │  │ - MCP bridge    │   │
│  └──────┬──────┘  └────────┬────────┘   │
│         │                  │            │
│         └────── messages ──┘            │
│                    │                    │
│  ┌─────────────────┴───────────────┐    │
│  │          Popup UI               │    │
│  │                                 │    │
│  │  - Current site score           │    │
│  │  - Quick actions (alias, block) │    │
│  │  - Policy status                │    │
│  │  - Link to full dashboard       │    │
│  └─────────────────────────────────┘    │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │     Dashboard (extension page)  │    │
│  │                                 │    │
│  │  - All services + scores        │    │
│  │  - Identity graph visualization │    │
│  │  - Audit log viewer             │    │
│  │  - Policy editor                │    │
│  │  - Kill switches per service    │    │
│  │  - Agent activity log           │    │
│  └─────────────────────────────────┘    │
│                                         │
│  Storage: chrome.storage.local          │
│  (encrypted vault blobs)                │
└─────────────────────────────────────────┘
```

### Content Script Behavior

**Form Interception:**
1. Detect `<form>` elements with email/phone/name input fields (heuristic: `type`, `name`, `autocomplete` attributes)
2. On detection, inject subtle UI: "Use Gatekeeper alias?" next to input
3. If user accepts: query background worker for alias, fill field
4. Log the grant as a permission token

**T&C Detection:**
1. On page load, check if URL matches known ToS page patterns (`/terms`, `/tos`, `/legal`, `/privacy`)
2. Also detect "I agree" checkboxes near links to terms
3. Inject score badge: "Gatekeeper Score: C (52/100)" with expandable detail
4. If terms have changed since last visit, show alert banner

### Storage Model

```
chrome.storage.local:
  gk_vault_encrypted: <AES-256-GCM blob>
  gk_policy: <JSON policy object>
  gk_tokens: <array of permission tokens>
  gk_audit_log: <array of audit entries>
  gk_settings: {
    passphrase_hash: <for unlock verification>,
    auto_alias: true,
    notifications: true,
    sync_enabled: false
  }
```

MV3 constraint: service worker can be killed anytime. All state must be in `chrome.storage`, never in memory. Alarms API for periodic tasks (T&C change checks).

---

## MCP Server Design

### Transport
- **Local:** stdio (agent runs MCP server as subprocess)
- **Remote:** SSE over HTTPS (for hosted/cloud agents)

### Tools

```typescript
// Tool definitions
const tools = [
  {
    name: "gatekeeper_check_service",
    description: "Look up T&C score and risk profile for a known service",
    inputSchema: {
      type: "object",
      properties: {
        service: { type: "string", description: "Service name or domain (e.g., 'spotify' or 'spotify.com')" }
      },
      required: ["service"]
    }
    // Returns: score, grade, risks, clauses, alternatives, opt-out URL
  },
  {
    name: "gatekeeper_evaluate_terms",
    description: "Score arbitrary Terms of Service text",
    inputSchema: {
      type: "object",
      properties: {
        text: { type: "string", description: "Raw ToS text to evaluate" },
        url: { type: "string", description: "URL of the ToS page (optional, for caching)" }
      },
      required: ["text"]
    }
    // Returns: score, grade, detected clauses, risks
  },
  {
    name: "gatekeeper_check_policy",
    description: "Check if a service's terms comply with a user's personal policy",
    inputSchema: {
      type: "object",
      properties: {
        service: { type: "string" },
        policy: { type: "object", description: "User policy object (or omit to use stored default)" }
      },
      required: ["service"]
    }
    // Returns: compliant (bool), conflicts (array), recommendation
  },
  {
    name: "gatekeeper_get_alternatives",
    description: "Get privacy-respecting alternatives to a service",
    inputSchema: {
      type: "object",
      properties: {
        service: { type: "string" },
        minGrade: { type: "string", default: "B" }
      },
      required: ["service"]
    }
    // Returns: array of alternatives with scores
  },
  {
    name: "gatekeeper_log_agreement",
    description: "Record that an agent agreed to terms on behalf of the user",
    inputSchema: {
      type: "object",
      properties: {
        service: { type: "string" },
        termsUrl: { type: "string" },
        fieldsShared: { type: "array", items: { type: "string" } },
        agentId: { type: "string" },
        policyCheck: { type: "boolean", default: true }
      },
      required: ["service", "agentId"]
    }
    // Returns: logId, policyCompliance (if checked), warnings
  },
  {
    name: "gatekeeper_audit",
    description: "Show all agreements made by agents on behalf of the user",
    inputSchema: {
      type: "object",
      properties: {
        since: { type: "string", description: "ISO date" },
        agentId: { type: "string", description: "Filter by agent" },
        flaggedOnly: { type: "boolean", description: "Only show policy violations" }
      }
    }
    // Returns: array of agreement records with policy compliance status
  }
];
```

### Auth
- **Local MCP (stdio):** No auth needed — runs as user's local process
- **Remote MCP (SSE):** API key in header (`Authorization: Bearer gk_...`)
- **Rate limits:** Free tier: 100 req/day. Developer tier ($29/mo): 10K req/day. Enterprise: custom.

### Package Structure
```
@gatekeeper/mcp-server
├── src/
│   ├── index.ts          # MCP server entry point
│   ├── tools/            # One file per tool
│   ├── scoring/          # T&C scoring engine
│   ├── policy/           # Policy evaluation engine
│   └── storage/          # Local audit log
├── package.json
└── README.md
```

Published as `@gatekeeper/mcp-server` on npm. Users add to their MCP config:
```json
{
  "mcpServers": {
    "gatekeeper": {
      "command": "npx",
      "args": ["@gatekeeper/mcp-server"],
      "env": { "GATEKEEPER_API_KEY": "gk_..." }
    }
  }
}
```

---

## API Design

### Base URL
`https://api.gatekeeper.id/v1`

### Endpoints

| Method | Path | Auth | Description |
|---|---|---|---|
| GET | `/scores/{service}` | Optional | Get score for a service |
| GET | `/scores` | Optional | List/search scores (query params: category, grade, q) |
| GET | `/compare` | Optional | Compare two services (params: a, b) |
| POST | `/evaluate` | Required | Score raw ToS text |
| GET | `/alternatives/{service}` | Optional | Get alternatives |
| GET | `/changes` | Optional | Recent T&C changes across all services |
| GET | `/changes/{service}` | Optional | Change history for one service |
| POST | `/policy/check` | Required | Check service against user policy |

### Auth
- **Public endpoints** (GET scores, compare, alternatives): No auth, rate limited to 60 req/hr by IP
- **Authenticated endpoints** (evaluate, policy): API key (`X-API-Key: gk_...`)
- **Key tiers:**
  - Free: 100 req/day, public endpoints only
  - Developer ($29/mo): 10K req/day, all endpoints, evaluate up to 50K chars
  - Enterprise: Custom limits, SLA, bulk evaluate, webhook for changes

### Response Format
```json
{
  "ok": true,
  "data": { ... },
  "meta": {
    "cached": true,
    "lastUpdated": "2026-03-25T00:00:00Z",
    "requestId": "req_abc123"
  }
}
```

Error:
```json
{
  "ok": false,
  "error": {
    "code": "RATE_LIMITED",
    "message": "Free tier: 100 requests/day. Upgrade at gatekeeper.id/pricing",
    "retryAfter": 3600
  }
}
```

---

## Open Questions / Decisions Needed

### Critical

1. **Email relay: build vs. partner vs. integrate?**
   - Build own relay: full control, high operational burden (email deliverability is a nightmare)
   - Partner with SimpleLogin/Proton: proven infrastructure, revenue share, dependency
   - Use SimpleLogin API: quick to ship, they handle deliverability, we're a reseller essentially
   - **Leaning:** SimpleLogin API for v1, own relay for v2 if scale justifies

2. **Phone number aliases: how?**
   - Twilio virtual numbers: $1/mo per number, proven but expensive at scale
   - Partner with existing services (Hushed, Burner): less control
   - Skip phone aliases in v1, focus on email
   - **Leaning:** Skip in v1. Email aliases first. Phone in v2.

3. **T&C scoring: rule-based vs. LLM?**
   - Rule-based: deterministic, auditable, but brittle + high maintenance
   - LLM-based: flexible, handles novel clauses, but non-deterministic + cost
   - Hybrid: rules for known patterns, LLM for novel/ambiguous
   - **Leaning:** Hybrid. Rules for the obvious stuff (arbitration clause, data selling language). LLM (Claude API) for nuanced scoring.

4. **Database: D1 vs. Supabase?**
   - D1: Native to Cloudflare stack, SQLite, cheap, edge-native, but limited features
   - Supabase: Postgres, richer features, auth built-in, but another dependency
   - **Leaning:** D1. We're already on Cloudflare. Keep the stack simple.

### Important but not blocking

5. **Monetization of the graph data?**
   - Aggregate, anonymized data about service terms and data flows is incredibly valuable
   - Could sell to regulators, researchers, compliance teams
   - Privacy implications of aggregating even anonymized data
   - **Decision needed by:** Phase 4

6. **Extension: paid features enforcement?**
   - Extension code is client-side, easily cracked
   - Options: server-side validation of Pro status, or just accept some leakage
   - **Leaning:** Server-side validation for alias generation and change alerts. Dashboard features can be client-side.

7. **Multi-browser support timeline?**
   - Firefox, Safari, Edge
   - MV3 works on Firefox (with minor changes) and Edge (Chromium)
   - Safari requires WebExtension conversion
   - **Leaning:** Chrome first. Firefox fast-follow (same codebase, minor manifest changes). Safari later.

8. **Naming: "Gatekeeper" trademark risk?**
   - Apple uses "Gatekeeper" for macOS security
   - EU DMA uses "gatekeeper" for big tech companies
   - Need trademark search before committing
   - **Action:** Run trademark search in software/security categories

---

*This is a living document. Update it when decisions are made. If something here is wrong, fix it — don't leave stale specs lying around.*
