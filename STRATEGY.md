# Gatekeeper — Strategy

**Last updated:** 2026-03-25
**Status:** Internal — not for external distribution

---

## Why This Matters Now

Two tectonic shifts are converging:

1. **AI agents are about to become the primary way humans interact with services.** Claude, GPT, Gemini — they're all getting "computer use" and autonomous action. Within 18 months, your AI agent will sign up for services, agree to terms, and share your identity data on your behalf. At machine speed. 50 agreements/day instead of a human's 5/week.

2. **There is no policy layer between your agent and the world.** No framework exists for an agent to ask "should I agree to this?" or "does my human allow this service to train AI on their data?" Agents will just... click "I agree" and move on.

This creates a market for identity sovereignty infrastructure. Not in 5 years. Now. The agent frameworks (MCP, OpenAI function calling, LangChain tools) are shipping today. The policy layer needs to ship alongside them.

Gatekeeper is that policy layer.

---

## Target Users

### Primary: Developers and AI Agent Builders (Phase 2)
- Building agents that interact with web services
- Need T&C intelligence as an API/MCP tool
- Want to advertise "privacy-aware" agents
- Willing to pay $29/mo for reliable data
- **Where they are:** GitHub, HN, MCP directories, agent framework docs

### Secondary: Privacy-Conscious Consumers (Phase 3)
- Already use ad blockers, VPNs, privacy tools
- Will install a Chrome extension
- Willing to pay $9/mo for convenience
- **Where they are:** r/privacy, HN, privacy newsletters, tech press

### Tertiary: Enterprise / Compliance (Phase 4)
- Agent platforms that need to demonstrate responsible data handling
- Companies under GDPR/eIDAS compliance pressure
- **Where they are:** Enterprise sales, conferences, compliance forums

### Who this is NOT for (yet)
- Average consumer who doesn't know what ToS means
- This becomes their tool only when it's invisible — embedded in browsers, agents, and platforms
- That's the endgame, not the launch

---

## Competitive Positioning

### Detailed Matrix

```
                    T&C Intel  API/MCP  Agent    Identity  Revocation  Email
                    + Scoring  Access   Integr.  Graph     Controls    Aliases
                    --------  -------  -------  --------  ----------  -------
Gatekeeper           ✓          ✓        ✓        ✓         ✓          ✓
SimpleLogin          ✗          ✗        ✗        ✗         partial    ✓
Firefox Relay        ✗          ✗        ✗        ✗         partial    ✓
Apple Hide My Email  ✗          ✗        ✗        ✗         ✓          ✓
ToS;DR               partial    ✗        ✗        ✗         ✗          ✗
Privacy.com          ✗          ✗        ✗        ✗         ✓          ✗ (cards)
DuckDuckGo Email     ✗          ✗        ✗        ✗         partial    ✓
```

### Where each competitor falls short

**SimpleLogin (Proton):** Best email aliasing service. But that's all it does. No intelligence about what you're signing up for, no policy engine, no agent integration. It's a privacy tool, not a sovereignty tool.

**Firefox Relay:** Mozilla's entry. Email + phone masking. No scoring, no graph, no API. Limited to Firefox users.

**Apple Hide My Email:** Excellent UX but Apple-only. No cross-platform. No terms analysis. No agent integration. Apple has no incentive to tell you their own terms are predatory.

**ToS;DR:** Closest to our T&C scoring. But: crowdsourced (stale, incomplete), no API, no MCP integration, no agent support, no identity management. A wiki, not a tool.

**Privacy.com:** Virtual credit cards. Financial layer only. No identity management, no terms analysis.

### Our moat (in order of defensibility)

1. **T&C intelligence dataset** — Scored, structured, diff-tracked terms for hundreds of services. This is the hardest thing to replicate because it requires ongoing scraping, analysis, and maintenance.

2. **MCP/agent integration** — First mover in the "agent policy layer" space. Network effects: as more agents use Gatekeeper, the data gets better, which attracts more agents.

3. **Identity graph** — Over time, the aggregate data about where data flows (Service A → Broker X → Service B) becomes uniquely valuable. No competitor is building this.

4. **Protocol lock-in** — If the permission language becomes a standard, Gatekeeper is the reference implementation. Hard to displace.

### What's NOT a moat
- Email aliases (commodity — SimpleLogin, Firefox Relay, Apple all do this)
- Chrome extension (anyone can build one)
- The website/index (content can be copied)

---

## Distribution Strategy

### Phase 1: HN Launch (March-April 2026)
**Goal:** 10K visitors, 1K waitlist, establish credibility

- Post: "We scored the terms of service for 30 services — here's what we found"
- Be active in comments for 24h
- Follow up with Show HN when API launches
- **Why HN:** Developer audience, privacy-conscious, high sharing velocity, sets narrative

### Phase 2: MCP Ecosystem (April 2026)
**Goal:** 500+ installs, integration into agent workflows

- Publish `@gatekeeper/mcp-server` on npm
- Submit to every MCP directory (Claude MCP repo, awesome-mcp lists, Smithery)
- Write tutorial: "How to make your AI agent privacy-aware in 5 minutes"
- Post on r/ClaudeAI, r/LocalLLaMA, agent dev communities
- Get mentioned in agent framework docs/tutorials

### Phase 3: Chrome Web Store (May-June 2026)
**Goal:** 10K installs, press coverage

- Product Hunt launch
- Privacy newsletter features (The Markup, EFF newsletter, WIRED)
- r/privacy, r/chrome, r/firefox
- Tech press pitch: "The first Chrome extension that reads terms of service for you"

### Phase 4: Enterprise + Press (Q3-Q4 2026)
**Goal:** B2B contracts, standards conversation

- Direct outreach to agent platform companies
- Conference talks (DEF CON, privacy conferences)
- EU regulatory engagement (eIDAS 2.0 context)
- Partnership announcements

### What we're NOT doing
- Paid ads (no budget, wrong audience)
- Social media marketing (low ROI for dev tools)
- Cold outreach to consumers (they come through press + organic)

---

## Revenue Model

### Assumptions
- T&C index gets 10K visitors → 5% waitlist conversion = 500 signups
- 10% of developer signups convert to paid = 50 × $29 = $1,450/mo (Phase 2)
- Extension gets 10K installs, 5% Pro conversion = 500 × $9 = $4,500/mo (Phase 3)
- 2-3 enterprise contracts at $5K-20K/mo (Phase 4)

### Revenue by Phase

```
Phase 2 (API):
  Free tier:        Unlimited (rate limited)
  Developer $29/mo: Target 50 users by month 3    = $1,450/mo
  Revenue:          ~$1,500/mo

Phase 3 (Extension):
  Free tier:        5 services
  Pro $9/mo:        Target 500 users by month 3   = $4,500/mo
  Revenue:          ~$4,500/mo (cumulative: $6,000/mo)

Phase 4 (Enterprise):
  B2B licensing:    $5K-20K/mo per contract
  Target:           2-3 contracts
  Revenue:          ~$20K-40K/mo (cumulative: $26K-46K/mo)

12-month target: $30K/mo
```

### Honest assessment
- Phase 2 revenue is realistic if the MCP ecosystem takes off
- Phase 3 depends heavily on extension UX and whether form interception actually works reliably
- Phase 4 is speculative — enterprise sales take longer than you think
- $30K/mo in 12 months is aggressive but achievable if Phase 4 lands even one contract
- Without enterprise: ~$6K/mo is more realistic. Good but not life-changing.

---

## Risk Register

### Existential Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| **Google/Apple builds this in** | Medium | Fatal | Move fast, establish protocol standard before they do. They profit from the broken system, so they might not. |
| **MCP/agent ecosystem doesn't grow** | Low-Medium | High | MCP is already adopted by Claude, Cursor, Windsurf. Trend is clear. But timeline could be longer than expected. |
| **T&C scoring is legally challenged** | Low | Medium | First Amendment protects editorial commentary. ToS;DR has operated for 12+ years. |
| **Nobody pays for privacy tools** | Medium | High | History says most people won't pay. Counter: developers will pay for API access (B2B > B2C). |
| **Email relay liability** | Low | Medium | Use SimpleLogin API (their liability). Don't build own relay in v1. |
| **"Gatekeeper" trademark conflict** | Medium | Medium | Apple uses the name for macOS. Run trademark search NOW. |

### Operational Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Solo founder burnout | High | High | Scope ruthlessly. Use AI for leverage. Don't try to do Phase 4 alone. |
| T&C data goes stale | Medium | High | Automated scraping pipeline (Phase 1). Manual review cadence. |
| Chrome Web Store rejects extension | Low-Medium | Medium | Follow MV3 guidelines strictly. No surprises in review. |
| Scraping blocked by services | Medium | Medium | Rotate user agents, use headless browsers, cache aggressively |
| Free tier abuse | Medium | Low | Rate limiting, CAPTCHA for evaluate endpoint |

---

## The Movement Angle

This is bigger than a product. This is about a fundamental question: **who controls your identity?**

Right now, the answer is "everyone except you." You give your email to 200 services. You can't un-give it. You can't see who they shared it with. You can't set terms. You can't audit anything.

Gatekeeper's long-term vision isn't a Chrome extension or an API. It's a protocol — like HTTPS, but for identity. Something so fundamental that it becomes invisible infrastructure.

### How to talk about it
- Not "privacy tool" (boring, commoditized, people tune out)
- Not "data protection" (sounds like compliance paperwork)
- **"Identity sovereignty"** — you own your identity the way you own your house
- **"DNS for human identity"** — your reachability is a function you control
- **"The policy layer for the agent era"** — the hook for developers

### The honest problem with the movement angle
- Average person won't care until something bad happens to them personally
- "I have nothing to hide" is still the dominant mindset
- Apple and Google profit from the current system and control the browser/OS chokepoints
- This needs a catalyst: massive breach, AI impersonation scandal, or government mandate

### Catalysts to watch
- **AI agent impersonation** (12-18 months) — agent signs up as you, leaks your data
- **EU eIDAS 2.0 implementation** (2026-2027) — mandates digital identity wallets
- **Next Equifax-scale breach** — could happen any time
- **FTC action on AI agent consent** — regulatory pressure on agent companies

---

## Partnership Opportunities

### High Priority

**Proton (SimpleLogin)** — Email relay infrastructure. They have the tech, we have the intelligence layer. Partnership: their aliases + our scoring/policy engine. Revenue share possible.

**Agent framework companies (Anthropic, LangChain, CrewAI)** — Get Gatekeeper listed as a recommended MCP tool for responsible agent behavior. Co-marketing: "Build privacy-aware agents with Gatekeeper."

**Mozilla** — Values-aligned. Firefox integration could be massive distribution. Mozilla Ventures might fund this.

### Medium Priority

**EFF** — Endorsement would be massive for credibility. They care about this space but don't build products.

**Consumer Reports** — They do product ratings. We do service ratings. Natural collab for a joint report on predatory terms.

**Privacy-focused VPN/browser companies (Mullvad, Brave)** — Bundle opportunities. Brave browser + Gatekeeper extension = strong story.

### Speculative

**EU regulatory bodies** — If eIDAS 2.0 creates demand for identity infrastructure, Gatekeeper could be a reference implementation.

**Insurance companies** — Identity theft insurance + Gatekeeper prevention layer. Long shot but worth exploring.

---

## What Success Looks Like

**6 months (September 2026):**
- T&C index is a known resource (cited by journalists, linked by privacy communities)
- MCP server has 1K+ installs
- Chrome extension has 5K+ users
- Revenue: $3-5K/mo
- 1-2 enterprise conversations started

**12 months (March 2027):**
- Protocol spec published
- 2+ agent platforms integrated
- Extension at 20K+ users
- Revenue: $15-30K/mo
- Recognized as "the identity sovereignty project" in agent/privacy intersection

**24 months (March 2028):**
- Protocol adopted as de facto standard for agent identity handling
- Enterprise contracts generating majority of revenue
- Team of 3-5 people
- Revenue: $50K+/mo
- Regulatory engagement (EU, FTC)

Or it doesn't work, and that's fine too. The T&C index and MCP tools are useful regardless. The protocol is the moonshot.

---

*This is an internal strategy doc. Be honest in it. Update it when the world changes.*
