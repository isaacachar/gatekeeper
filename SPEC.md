# Gatekeeper — The Predatory T&C Index

## What This Is
A single-page landing site that ranks the most popular online services by how predatory their Terms & Conditions are. Think "The Dirty Dozen" meets Consumer Reports — editorial, sharp, shareable.

## Design Direction
- **Tone:** Editorial. Think investigative journalism meets data visualization. Not corporate SaaS.
- **Aesthetic:** Dark, serious, high-contrast. This is about exposing bad actors — the vibe should feel like a confidential dossier or leaked document.
- **Typography:** Bold, newspaper-editorial feel. Monospace accents for data/scores. Serif or strong sans for headlines.
- **Layout:** Magazine-style. Hero section with a provocative headline, then ranked cards/rows.
- **NO:** Emojis in UI, gradients, rainbow elements, generic SaaS look, purple anything.
- **YES:** Sharp contrast, red accents for danger/violations, clean data presentation, generous whitespace.

## Structure

### 1. Hero Section
- Headline: Something provocative like "You agreed to all of this." or "What you signed away today."
- Subhead: Brief explanation — we read the terms so you don't have to.
- Simple stat: "We analyzed X services. Here's what we found."

### 2. Key Stats Bar
- 3-4 shocking aggregate stats pulled from the data
- e.g., "87% sell your data to third parties" / "62% can change terms without notice" / "41% claim ownership of your content"

### 3. The Index (Main Content)
Ranked list of services, worst first. For each:
- **Rank + Company name + Logo placeholder** (use first letter as avatar)
- **Grade:** A through F (color-coded: A=green, F=red)
- **Score:** Numeric (0-100, lower = worse)
- **Worst Clauses:** 2-3 bullet points in plain English
- **Categories/Tags:** Data selling, Content ownership, Forced arbitration, No deletion, Terms change without notice, etc.

Make it filterable by category. Searchable.

### 4. "What This Means" Section
- Brief explainer on methodology
- What each grade means
- Why this matters (especially in an AI agent future — your agent will agree to these 50x/day)

### 5. CTA / Waitlist
- "We're building a tool that reads these for you. Automatically."
- Email signup for the Gatekeeper extension waitlist
- Simple form, no fluff

### 6. Footer
- Gatekeeper branding
- "Open data. Open methodology."
- Links: GitHub (placeholder), Methodology, Contact

## Technical
- Single HTML file with embedded CSS/JS (no build step)
- Tailwind via CDN is fine
- Mobile responsive
- Data: Hardcode ~30 services with realistic T&C analysis data as a JSON array in the file
- Make it easy to update the data later (clear data structure at top of script)
- Include filter/search functionality (JS)
- Dark mode by default, optional light toggle

## Services to Include (rank by how bad they are)
Include a mix — some terrible (F), some okay (B/C), a couple good (A) to show the range.

Categories of clauses to evaluate:
1. Data selling to third parties
2. Content ownership/licensing
3. Forced arbitration (no class action)
4. Terms can change without notice
5. Account deletion difficulty
6. Data retention after deletion
7. Location tracking
8. Cross-platform data sharing
9. Right to modify/use content for AI training
10. Liability limitations

## Data Notes
Create REALISTIC but clearly labeled as editorial analysis. Each service should have:
- name, category (social, messaging, shopping, etc.)
- overallScore (0-100)
- grade (A-F)
- worstClauses: array of plain-English summaries
- tags: array of violation categories
- lastReviewed: date string
- highlight: one-sentence "what this actually means" summary

Make the data provocative but defensible — these should reflect real known issues with these companies' actual terms (data selling, forced arbitration, content licensing, etc.). We're editorializing but based on reality.
