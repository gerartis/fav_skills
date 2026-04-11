---
name: ai-timeline-research
description: >
  Research and populate a structured competitive intelligence timeline in a specific
  JSON format, then perform a supervisory sense-check pass to catch errors, missing
  events, and thin business model coverage. Use this skill whenever the user wants
  to build, populate, or fact-check a timeline of AI or SaaS companies — including
  requests like "populate a timeline for these companies", "research the history of
  X and Y", "sense-check this timeline", "add funding rounds and product launches",
  or "build a competitive timeline". Also use when the user provides a list of
  companies and asks for a structured research output, even if they don't use the
  word "timeline". Works for any AI/SaaS domain: voice AI, music AI, coding tools,
  data infra, vertical SaaS, foundation models, etc.
---

# AI / SaaS Competitive Timeline Research Skill

Builds and validates a structured competitive intelligence timeline across multiple companies in any AI or SaaS domain. Runs in two phases: **Research** (populate events from scratch) and **Sense-Check** (supervisory review pass on an existing timeline). These can be run independently or sequentially.

---

## Step 0 — Intake (always run first)

Before doing anything else, ask the user:

1. **What domain / topic is this timeline for?**  
   e.g. "AI coding assistants", "data warehouse SaaS", "AI audio / voice", "foundation model providers", "vertical AI for legal", "browser automation"

2. **Which companies should be included?** (if not already provided)  
   If the user hasn't specified, suggest a sensible shortlist based on the domain — covering the key archetypes for that space (see Sector Taxonomy below).

3. **What phase are we running?**
   - **(A) Research & populate** a new timeline from scratch
   - **(B) Sense-check** an existing timeline (paste it in)
   - **(C) Both** — research first, then sense-check in a fresh context

4. **Any specific focus areas?**  
   e.g. "focus on model dynamics and GTM", "I care most about enterprise deals", "include the legal landscape", "track distribution partnerships"

Once you have the domain and company list, derive the **sector taxonomy** for that space before writing a single line of JSON.

---

## Sector Taxonomy (derive per domain)

Different domains have different structural layers. Before populating, map the landscape:

**Generic archetypes to consider for any AI/SaaS domain:**

| Archetype | Description | Example (voice AI) | Example (coding AI) |
|---|---|---|---|
| **Foundation / Model** | Trains and serves the core models | MiniMax, ByteDance | Anthropic, OpenAI |
| **Infrastructure / API** | Developer-facing model APIs, infra layer | Deepgram, Cartesia | GitHub Copilot backend, Fireworks |
| **Application / Consumer** | End-user products built on models | Suno, ElevenReader | Cursor, Replit |
| **Platform / Distribution** | Controls access to large user bases | TikTok, YouTube | VS Code, JetBrains |
| **Vertical / Specialist** | Deep focus on one industry or use case | Audioshake (stem sep), OfOne (QSR) | Codeium (enterprise), Sweep (GitHub) |
| **Tooling / Workflow** | Integrates into existing professional tools | iZotope (DAW plugins) | Tabnine (IDE plugins) |
| **Data / Content** | Provides training data, licensed content, or datasets | Artlist, Epidemic Sound | The Stack, Common Crawl |
| **Enterprise Platform** | Sells AI capabilities into large orgs | Stability AI (WPP) | Harvey (legal), Glean |

Use these archetypes to assign `sector` labels that are meaningful for the specific domain. Don't force voice AI labels onto a coding AI timeline — derive fresh labels. The goal is that a reader can scan the sector column and immediately understand the structural role each player occupies.

**Suggested colour families by archetype** (adapt as needed):
- Foundation / model players: deep purples / indigos (`#5C4B9E`, `#7F77DD`, `#9B59B6`)
- Infra / API layers: blues / teals (`#378ADD`, `#5DCAA5`, `#2980B9`)
- Application / consumer: reds / oranges / warm tones (`#D85A30`, `#E74C3C`, `#E67E22`)
- Platform / distribution: ambers / dark greens (`#BA7517`, `#3B6D11`, `#27AE60`)
- Vertical / specialist: greys / muted (`#B4B2A9`, `#888780`, `#95A5A6`)
- Tooling / workflow: slate / dark (`#444441`, `#566573`, `#2C3E50`)

---

## Output Format

```js
const DATA=[
{id:"company-slug", name:"Display Name", sector:"Sector Label", color:"#hexcolor", events:[
  {date:"YYYY-MM", type:"EVENT_TYPE", title:"Short title", desc:"Full description with context."},
  ...
]},
...
];
```

### Field reference

| Field | Notes |
|---|---|
| `id` | lowercase-hyphenated slug, unique per company |
| `name` | Display name |
| `sector` | Derived from domain taxonomy above — meaningful for the specific space |
| `color` | Hex colour; visually distinct per company, grouped by archetype family |
| `date` | `"YYYY-MM"` — use `"YYYY-01"` if only year is known |
| `type` | See event types below |
| `title` | ≤8 words; punchy |
| `desc` | 1–4 sentences. **Must include strategic/business model context** — not just what happened, but why it matters |

### Event types

| type | Use for |
|---|---|
| `product` | Product launches, model releases, new features, founding |
| `funding` | Funding rounds, valuations, ARR milestones |
| `ma` | Acquisitions, mergers, strategic partnerships, licensing deals |
| `leadership` | Exec hires, departures, acqui-hires, board changes |
| `legal` | Lawsuits, settlements, regulatory actions |

---

## Phase 1 — Research & Populate

### Step 1 — Research plan (per company)

Before searching, identify what you're looking for:
- Founding date, founders, origin thesis
- All funding rounds (amount, lead investor, valuation, ARR if disclosed)
- Major product launches and model milestones
- Acquisitions, partnerships, distribution deals
- Leadership changes with strategic significance
- Legal/regulatory events (especially in regulated or IP-heavy domains)
- GTM moves: API programs, platform embeds, enterprise deals, channel partnerships

### Step 2 — Web research (3–5 searches per company)

Prioritise:
1. `[Company] funding rounds history`
2. `[Company] product launches milestones [year]`
3. `[Company] partnerships acquisitions`
4. `[Company] revenue ARR valuation [current year]`
5. `[Company] legal` or `[Company] [domain-specific risk]` if relevant

Cross-reference funding figures. When amounts conflict, prefer the company's own press release or a primary outlet (TechCrunch, The Information, domain-specific trade press) over aggregators like Crunchbase or Tracxn.

### Step 3 — Populate with strategic depth

For every event, ask before writing the `desc`:
- Is this a **new revenue category** or category expansion?
- Does this signal a **GTM / distribution strategy** shift?
- Does this change a **model-layer dependency** (e.g. building own model vs. wrapping OpenAI)?
- Is this a **partnership between a model provider and an app layer** — who controls the relationship?
- Is this a **vertical integration** move (infra player going up-stack, or app player going down-stack)?
- What does this reveal about **competitive moat or positioning**?

Encode the reasoning in `desc`. Examples of thin vs. rich descriptions:

> ❌ `"Led by Andreessen Horowitz."` — no context  
> ✅ `"Co-led by Nat Friedman, Daniel Gross, and a16z. Nat/Daniel as co-leads signals personal credibility bet alongside institutional capital. $X ARR at close. Funds model research and international expansion."` — has context

> ❌ `"Microsoft partnership announced."` — no context  
> ✅ `"Distributed inside Microsoft Copilot before own product had scale — Microsoft controls distribution, company owns the model. Word-of-mouth virality drove early growth without paid acquisition. Classic model provider ↔ platform distribution dynamic."` — has context

> ❌ `"Acquired [startup]."` — no context  
> ✅ `"Acquired [startup] (YC-backed, [domain]). First vertical expansion beyond core infra — signals willingness to own the end-market, not just the API layer. [Company]'s first move from picks-and-shovels toward application ownership."` — has context

---

## Phase 2 — Sense-Check (Supervisory Review)

**Run this in a fresh context window** — no prior research history. This removes confirmation bias from the research phase and surfaces errors invisible to the person who just wrote the content.

Paste in the existing timeline JSON and work through each company systematically.

### Sense-check checklist per company

**Factual accuracy**
- [ ] Funding amounts match primary sources
- [ ] Lead investors correctly identified — many rounds have 2–3 co-leads; naming only the most famous is a common error
- [ ] Dates correct to month, not just year
- [ ] Founding team complete — no co-founders omitted or mislabelled as "advisors" or "collaborators"
- [ ] Valuation figures are post-money (flag if unclear)
- [ ] ARR figures tied to a specific point in time ("at time of raise", "as of [month]")
- [ ] Acquisition targets correctly named and described

**Business model & strategic coverage**
- [ ] Does each funding entry note what the capital signals or funds?
- [ ] Are GTM / distribution moves captured (API programs, platform embeds, enterprise channel deals)?
- [ ] Are model-layer dependencies flagged (who provides the underlying model? who controls distribution)?
- [ ] Is category expansion called out explicitly?
- [ ] Are deal/settlement terms noted for legal and M&A entries — not just "deal announced" but the structure?
- [ ] Are revenue model shifts noted (B2C → B2B, open → licensed, self-serve → enterprise)?
- [ ] Are vertical integration moves flagged (infra going up-stack, app going down-stack)?

**Completeness**
- [ ] Founding event present?
- [ ] All public funding rounds present?
- [ ] Major model / product releases present?
- [ ] Key legal events present (especially in IP-heavy domains)?
- [ ] Leadership changes with strategic significance present?

### Sense-check output format

Report findings in three tiers:

**🔴 Errors** — factual mistakes that must be fixed (wrong amounts, wrong dates, wrong lead investors, missing co-founders, wrong company descriptions)

**🟡 Inconsistencies** — internal conflicts (total raised doesn't add up across entries; ARR figure appears twice with different values; valuation contradicts a later entry)

**🟢 Missing / thin** — events that should be added, or `desc` fields that need strategic context added

For each finding, provide:
- Company + event title affected
- What the current entry says
- What it should say, with source if verifiable
- Suggested corrected or new JSON snippet

---

## Sample Events (style reference)

Well-formed events across all five types. Note how each `desc` encodes *why it matters*, not just *what happened*.

```js
// FUNDING — co-led round, ARR, strategic signal, GTM shift
{date:"2026-02", type:"funding", title:"$500M Series D → $11B",
 desc:"Led by Sequoia (Andrew Reed joins board). a16z quadrupled, ICONIQ tripled. New: Lightspeed, Evantic Capital, BOND. $330M ARR at close; total funding $781M. Revenue split ~50/50 enterprise/consumer — targeting 70/30 enterprise by end 2027. IPO signalled. Marks shift from developer-led growth to enterprise platform play."},

// PRODUCT — model release with competitive positioning and moat
{date:"2025-10", type:"product", title:"Sonic 3 — 42 languages",
 desc:"42-language TTS. Sub-90ms standard / ~40ms Turbo. Laughter and emotion tags. On-prem/private VPC deployment (SOC2, HIPAA, GDPR) differentiates from cloud-only competitors. SSM architecture enables constant-time inference — viable at edge and on-device, not just data centre."},

// M&A — acquisition with vertical strategy signal
{date:"2026-01", type:"ma", title:"Acquired OfOne",
 desc:"YC-backed voice AI for QSR drive-throughs (93%+ order accuracy). First vertical expansion beyond pure API infra — signals willingness to own the end-market. Classic infra-going-up-stack move: margin expansion, but also channel conflict risk with existing QSR customers building on the API."},

// M&A — partnership with dependency / power dynamics noted
{date:"2023-12", type:"ma", title:"Microsoft Copilot integration",
 desc:"Distributed inside Microsoft Copilot at launch — Microsoft controls distribution, company owns the model. Reached millions of users before building own web funnel. Classic platform distribution bet: fast top-of-funnel, but creates dependency on Microsoft's roadmap and terms."},

// LEGAL — settlement with structural terms, not just "settled"
{date:"2025-11", type:"legal", title:"WMG settlement + licensing deal",
 desc:"Warner Music lawsuit settled; licensed partnership formed. Walled-garden structure: users can create inside the platform but cannot export AI-generated output. No admission of liability. Sets precedent for label settlements in this domain — licensed use + platform containment, rather than cash payment."},

// LEADERSHIP — acqui-hire with model-layer significance
{date:"2026-01", type:"leadership", title:"Google DeepMind acqui-hire",
 desc:"CEO + ~7 engineers join Google DeepMind via non-exclusive technology license; company continues independently under new CEO. Signals Google treating emotional voice as a distinct model capability worth paying for — not just acquiring the team. Validates the thesis that emotional intelligence is a separable, licensable AI layer."},

// PRODUCT — platform architecture announcement (not just a feature)
{date:"2026-02", type:"product", title:"Three-pillar platform announced",
 desc:"Reorganised into enterprise agents, creative tools, and developer API pillars. Architecture signal: separating enterprise GTM from developer flywheel from consumer creative — mirrors Stripe/Twilio playbook. Each pillar has different pricing, buyer, and sales motion."},
```

---

## Common error patterns (check explicitly)

These recur across AI/SaaS timelines regardless of domain:

- **Co-lead misattribution** — rounds often have 2–3 co-leads; aggregators and press frequently credit only the most prominent. Always check the company's own announcement.
- **Month-level date errors** — secondary sources often round to quarters or mid-year. Verify against press releases for exact month.
- **Founding team omissions** — less prominent co-founders (especially academic or technical) are frequently dropped from secondary sources. Check company "About" pages and early press.
- **"Advisor" vs. co-founder** — academic spinouts often have a senior professor as full co-founder who gets downgraded to "advisor" or "research collaborator" in aggregator databases.
- **ARR without a timestamp** — "$100M ARR" is meaningless without knowing when. Always tie to a point in time.
- **Valuation ambiguity** — post-money vs. pre-money; primary round valuation vs. secondary/tender. Flag when the source is unclear.
- **Deal structure omitted** — for M&A and legal entries, the *structure* is often more strategically significant than the headline. "Acquired" is incomplete; "acqui-hire with non-exclusive license, company continues independently" is complete.
- **Internal inconsistency** — total raised figures across entries don't add up; ARR cited differently in two entries for the same company. The sense-check pass should catch these specifically.
