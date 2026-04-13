---
name: timeline-builder
description: >
  Use this skill whenever the user wants to research and build a structured competitive or historical timeline on ANY topic — companies, industries, technologies, historical periods, scientific fields, sports franchises, geopolitical events, or anything with a chronological narrative. Triggers include: "build me a timeline of X", "research the history of Y", "create a competitive timeline for Z", "I want to track how [topic] evolved", "map out the key events in [domain]", "research [companies] and put them in a timeline", or any request that involves investigating multiple entities or a topic over time and producing structured chronological output. The output is a .js file that can be loaded directly into the timeline viewer. Always use this skill for timeline research tasks — it produces dramatically better output than ad-hoc searching, because it includes a mandatory sense-check pass in a fresh context to catch factual errors and thin coverage.
---

# Timeline Builder Skill

Researches any topic deeply, structures findings as richly-annotated chronological events, then performs a supervisory fact-check pass in a fresh context before producing a `.js` file loadable in the timeline viewer.

The skill has two phases:
- **Phase 1 — Research & Populate**: Deep web research, structured into events with strategic/contextual depth
- **Phase 2 — Sense-Check**: Fresh-context review to catch errors, inconsistencies, and thin coverage

---

## Step 0 — Intake

Before any research, ask the user (use `ask_user_input` tool if available, otherwise ask in prose):

1. **What is the topic?**  
   Can be anything: a set of companies, an industry, a technology, a historical period, a sports franchise, a scientific field, a geopolitical region, etc.

2. **What are the entities to track?**  
   - For competitive/company timelines: which companies or organizations?
   - For a single subject: what sub-threads to track? (e.g. for a country's history: politics, economy, culture, military)
   - If the user isn't sure, suggest a sensible set of 4–8 entities based on the topic

3. **What time range?**  
   - If not specified, infer a sensible range from the topic

4. **What type of events matter most?**  
   Default set (adapt to topic):
   - **product** — launches, releases, inventions, innovations, founding
   - **funding** — rounds, valuations, revenue milestones (for companies); budgets, economic data (for other topics)
   - **ma** — acquisitions, mergers, partnerships, alliances, treaties
   - **leadership** — key hires/departures, elections, transitions of power
   - **legal** — lawsuits, regulations, rulings, policies

   For non-company topics, remap these to domain-appropriate equivalents (see Domain Remapping below).

5. **Any specific focus areas or known gaps?**  
   e.g. "focus on the legal/IP angle", "I know the early history but need the last 3 years", "pay attention to international developments, not just US"

---

## Domain Remapping

The five event types are universal containers. For non-corporate topics, remap them:

| Default type | History / Politics | Science / Technology | Sports | Culture / Media |
|---|---|---|---|---|
| `product` | Policy enacted, law passed, invention | Discovery, paper published, tool released | Game/match result, record broken | Album/film/book released |
| `funding` | Treaty signed, budget allocated, economic milestone | Grant awarded, lab funded | Contract signed, valuation | Box office, streaming deal |
| `ma` | Alliance formed, merger of states, annexation | Lab merger, acquisition of IP | Team trade, franchise acquired | Studio acquisition, merger |
| `leadership` | Election result, coup, resignation | Nobel awarded, leadership change | Coach hired/fired, captain change | Award won, editorial change |
| `legal` | War declared, sanctions, court ruling | Patent filed/contested, ethics controversy | Rule change, scandal, ban | Censorship, rights dispute |

Use the type that best fits the underlying nature of the event. The viewer colors and labels by type, so a sensible mapping matters.

---

## Output Format

```js
const DATA = [
  {
    id: "entity-slug",
    name: "Display Name",
    sector: "Category Label",
    color: "#hexcolor",
    events: [
      {
        date: "YYYY-MM",
        type: "EVENT_TYPE",
        title: "Short title ≤8 words",
        desc: "1–4 sentences. What happened AND why it matters in context."
      }
    ]
  }
];
```

### Field rules

| Field | Rule |
|---|---|
| `id` | lowercase-hyphenated slug, unique per entity |
| `name` | Display name as it should appear in the viewer |
| `sector` | Short category label meaningful for this topic (not generic) |
| `color` | Visually distinct hex; see color guidance below |
| `date` | `"YYYY-MM"` always. Use `"YYYY-01"` if only the year is known |
| `title` | ≤8 words, punchy, no trailing punctuation |
| `desc` | Context-rich. See Depth Standard below. |

### Color guidance by entity role

Assign colors so a reader can see the structural role at a glance:

- **Primary / leading players**: deep saturated tones (`#1D9E75`, `#D4537E`, `#5C4B9E`)
- **Infrastructure / supporting players**: mid blues and teals (`#378ADD`, `#5DCAA5`)
- **Disruptors / challengers**: warm reds and oranges (`#D85A30`, `#E07040`)
- **Regulatory / legal / external forces**: desaturated reds and greys (`#922B21`, `#888780`)
- **Platform / distribution players**: ambers and greens (`#BA7517`, `#3B6D11`)

For non-company topics (historical periods, etc.), color by sub-theme instead (e.g. political = indigo, economic = green, military = red).

---

## Phase 1 — Research & Populate

### Step 1 — Research plan

For each entity, identify what to look for:
- Origin / founding / creation context
- Key milestones and turning points
- Relationships with other entities on the timeline (partnerships, conflicts, dependencies)
- Quantitative anchors (funding amounts, revenue, dates, vote counts, scores, whatever is appropriate)
- Why specific decisions were made — the strategic or causal logic, not just the facts
- Controversies, failures, pivots — not just the highlights

### Step 2 — Web research (3–6 searches per entity)

For companies and organizations:
1. `[Entity] history milestones`
2. `[Entity] funding rounds` or `[Entity] financial history`
3. `[Entity] acquisitions partnerships`
4. `[Entity] leadership changes`
5. `[Entity] legal controversies` (if relevant)
6. `[Entity] [current year]` for recent events

For historical / scientific / cultural topics:
1. `[Topic] timeline key events`
2. `[Topic] [sub-period or sub-theme] history`
3. `[specific event or person] date context`
4. Verify specific facts from primary or academic sources where possible

**Cross-reference all dates and figures.** When sources conflict, note it and prefer:
- Company press releases or official announcements over aggregators
- Primary historical sources over Wikipedia summaries
- Domain-specific journalism over general news

### Step 3 — The Depth Standard

Every `desc` must pass the "so what?" test. Ask before writing:

- **What caused this?** What context made this event happen?
- **What changed because of this?** How did it affect the entity or the field?
- **What does this reveal?** About strategy, intent, power dynamics, trajectory?
- **How does this connect** to other events or entities on this timeline?

**Examples of thin vs. rich descriptions:**

> ❌ `"Raised $50M Series B."` — just the fact  
> ✅ `"Raised $50M Series B led by [investor], bringing total to $73M. Round came 6 months after first enterprise contract; signals the company pivoting from self-serve to enterprise GTM. [Investor] brings SaaS distribution relationships that give them access to Fortune 500 procurement cycles."` — has cause, significance, forward implication

> ❌ `"Treaty of Versailles signed."` — just the label  
> ✅ `"Treaty of Versailles signed, formally ending WWI and imposing war guilt clause and reparations on Germany. The reparations terms — $33B — were seen as punitive by many economists including Keynes, who publicly warned they would destabilize Germany. Directly shapes the political resentment that enables the NSDAP's rise a decade later."` — has context, consequence, forward linkage

> ❌ `"Album released to positive reviews."` — useless  
> ✅ `"Debut album released, reaching #3 on the Billboard 200 and eventually going 4x platinum. Represented the commercial breakthrough of the West Coast hip-hop sound; introduced [artist] to mainstream radio audiences and triggered a wave of major-label signings of similar acts from the region."` — has numbers, context, industry effect

---

## Phase 2 — Sense-Check Pass

**Run this in a fresh context** — start a new conversation and paste in the Phase 1 output. This removes confirmation bias. The sense-checker has not done the research and will catch errors that the researcher glazed over.

### Sense-check checklist per entity

**Factual accuracy**
- [ ] Dates are correct to the month where verifiable
- [ ] Named figures (amounts, scores, vote counts, quantities) match primary sources
- [ ] People are correctly identified (full names, roles, titles)
- [ ] Relationships between entities are correctly described (ally vs. rival, acquirer vs. acquiree, etc.)
- [ ] No events attributed to the wrong entity

**Depth and context**
- [ ] Does each `desc` pass the "so what?" test?
- [ ] Are cause-and-effect relationships stated, not just implied?
- [ ] Are connections to other entities/events noted where relevant?
- [ ] Are any descriptions just restating the title with different words? (Flag as thin)

**Completeness**
- [ ] Is there a founding / origin event?
- [ ] Are there obvious major milestones missing? (Check against common knowledge and any list-type search results)
- [ ] Are recent events covered, or does the timeline trail off?
- [ ] Are failures and controversies represented, or only successes?
- [ ] Is the timeline balanced across entity types (not 15 events for one entity and 2 for another without reason)?

### Sense-check output

Report findings in three tiers before producing the final file:

**🔴 Errors** — factual mistakes to fix (wrong date, wrong amount, wrong person, wrong relationship)

**🟡 Inconsistencies** — internal contradictions (totals that don't add up, same event described differently in two entries, timeline ordering problems)

**🟢 Thin / missing** — events or context that should be added or expanded

For each finding, provide:
- Entity name + event title affected
- What the current entry says
- What it should say
- Corrected JSON snippet

After reporting, produce the corrected final output.

---

## Final Output

1. Save the corrected JS file as `[topic-slug]-timeline.js`
2. Present it to the user with `present_files`
3. Include a brief inline summary:
   - How many entities, how many total events
   - Date range covered
   - Any significant caveats (events where sources conflicted, things that couldn't be verified)
   - Suggested next searches if the user wants to deepen any entity

---

## Quality gates before delivering

Do not deliver until all of these are true:

- [ ] Every entity has a founding/origin event
- [ ] No entity has fewer than 4 events (if fewer, do more research or note the entity is thin)
- [ ] Every `desc` is at least 2 sentences
- [ ] No two entities share the same `color`
- [ ] All dates are in `"YYYY-MM"` format
- [ ] The sense-check pass has been run and all 🔴 errors are resolved
- [ ] The file has been tested: the array is valid JS (no trailing commas before `]`, no unescaped quotes in strings)

---

## Notes on scope

This skill works for any topic but the quality ceiling is set by what's publicly findable via web search. For:

- **Recent company history** (last 5 years): high confidence, most events are verifiable
- **Older company history** (pre-2018): moderate confidence, some dates and details may need flagging
- **Historical events** (pre-2000): varies widely; well-documented topics are reliable, obscure topics need more caveats
- **Ongoing/live events**: note that events from the last few months may be incomplete or preliminary

When confidence is low on a specific fact, encode that in the `desc`: "Reports suggest..." or "Date approximate — sources conflict between March and April."
