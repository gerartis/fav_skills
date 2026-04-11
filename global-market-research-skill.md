---
name: global-market-research
description: >
  Use this skill whenever the user wants to do market research on a technology space, industry, or trend — especially when they want to understand competitive differentiation, company "spikes" (what makes each player uniquely strong or recognizable), or the real landscape beyond surface-level summaries. Trigger this skill for requests like "research the X market", "who are the players in Y", "what's the competitive landscape for Z", "help me understand the AI agent space", "do a deep dive on [tech trend]", "what differentiates companies in [industry]", or any time someone wants a global, multi-source view of a technology market. This skill is especially important when the user explicitly wants non-US or Chinese/APAC/European perspectives included, or when they're worried about getting a shallow "everyone does the same thing" analysis. Always use this skill for market research tasks — it will produce dramatically better output than ad-hoc web searches.
---

# Global Market Research Skill

Produce a rich, differentiated market landscape report for a technology space. The goal is NOT to summarize what every company says about itself — it is to surface the *spikes*: the things each player is genuinely known for among real users, the perception gaps between regions, and the honest competitive picture.

---

## Step 0: Clarify Before Searching

Before any research, ask the user these questions (use the `ask_user_input` tool if available, otherwise ask in prose):

1. **Which industry/trend?** (e.g., "AI coding assistants", "vector databases", "humanoid robotics")
2. **Which regional perspectives matter most?**
   - US / Western sources
   - European sources (especially Germany, France, Nordics)
   - Chinese sources (Zhihu, 36氪, 量子位, Xiaohongshu, Bilibili, Baidu, ModelScope, etc.)
   - Japanese / Korean sources (Zenn, Qiita, Nikkei, TechCrunch Japan)
   - Southeast Asia / India
3. **Market layer focus** (can select multiple):
   - Model/infrastructure layer
   - Enterprise tools & applications
   - Consumer tools & applications
   - Developer tooling
4. **What do they already know?** (so you don't recap basics)

Only proceed to research once these are confirmed.

---

## Step 1: Research Strategy

Plan your search across **four source types** before executing:

### A. English/Western Sources
- Tech media: TechCrunch, The Information, Wired, MIT Tech Review, VentureBeat
- Community: Reddit (r/LocalLLaMA, r/MachineLearning, relevant subreddits), Hacker News, Product Hunt reviews
- Professional: LinkedIn posts, industry analyst summaries (Gartner, a16z memos, Sequoia essays)
- Academic/benchmark: arXiv, Papers With Code, Hugging Face leaderboards

### B. Chinese Sources (search these explicitly)
- **36氪 (36kr.com)** — Chinese TechCrunch equivalent, funding & startup news
- **量子位 (qbitai.com)** — AI-focused news & analysis
- **机器之心 (jiqizhixin.com)** — Deep AI/ML technical journalism
- **Zhihu (zhihu.com)** — Q&A discussions, real user opinions, professional perspectives
- **Xiaohongshu / 小红书** — Consumer sentiment, lifestyle tech adoption
- **Bilibili** — Tech explainer videos, community discussions
- **ModelScope** — Chinese open-source model hub, community activity
- **Baidu Index** — Search trend data
- **微信公众号 articles** (via web search with site restrictions or aggregators)

### C. Japanese/Korean Sources
- **Zenn (zenn.dev)** — Developer community articles
- **Qiita (qiita.com)** — Technical tutorials, community perception
- **Nikkei / 日経** — Enterprise/business angle
- **TechCrunch Japan** — Startup ecosystem
- **ITmedia, Impress** — Consumer tech media

### D. Usage & Traction Signals (search explicitly for each major player)
- DAU/MAU announcements or leaks
- App store rankings (US, China, Japan, EU)
- API call volume signals
- GitHub stars, forks, contributor velocity
- Job posting trends (signal of investment areas)
- Enterprise contract announcements

---

## Step 2: Execute Research

Run **at minimum 12-15 web searches** across the source types above. For each major company/player identified:

1. Search their name + "review" or "vs" in English
2. Search their name in Chinese (e.g., `[company] 评测` or `[company] 怎么样`)
3. Search their name + `site:zhihu.com` or `site:36kr.com`
4. Search for their name + `site:zenn.dev` or Japanese language query

Do **not** stop after finding a company's official positioning. Look for:
- What users *complain about* (reveals real weaknesses)
- What users *rave about* in passing (reveals actual spikes)
- What competitors' users say when comparing

---

## Step 3: Identify Spikes

For each major player, synthesize a **spike**: the one or two things that make them genuinely distinctive in the eyes of real users — not in their own marketing.

**Spike framing rules:**
- Must be observable from user/community behavior, not press releases
- Must differentiate from at least 2 other players in the space
- Should note if the spike is perceived differently across regions
- Flag if the spike is a *perception* spike vs a *capability* spike (these differ)

Example spike formats:
> **Cursor** — Spike: "Tab to accept entire multi-file edits." Users specifically call out the diff UI as meaningfully faster than alternatives. Chinese devs on Zhihu praise its performance on Chinese-language codebases.

> **Perplexity** — Spike: "Search engine that cites sources inline, not a chatbot." Consumer perception in US leans toward replacing Google for research; Japanese users (Qiita threads) use it heavily for technical documentation search.

---

## Step 4: Structure the Output

Produce a Markdown file with this structure:

```markdown
# [Industry/Trend] — Global Market Landscape
*Research date: [date] | Sources: US/EU/CN/JP*

## TL;DR — The Honest Picture in 5 Bullets
[5 things a smart person would say after 3 months using the space]

## Market Map
[Brief orienting paragraph — what layers exist, what's competed vs. greenfield]

### Model / Infrastructure Layer
[Players, spikes, differentiation]

### Enterprise Applications
[Players, spikes, differentiation]

### Consumer Applications
[Players, spikes, differentiation]

### Developer Tooling
[Players, spikes, differentiation — only if applicable]

## Company Profiles

### [Company Name]
- **Founded / HQ**: 
- **Funding / Valuation**: [include round, lead investor, date]
- **Spike**: [1-2 sentence honest differentiation]
- **What users say (US/EU)**: [synthesized from forums/reviews]
- **What users say (CN)**: [synthesized from Chinese sources]
- **What users say (JP/KR)**: [if found]
- **Usage signals**: [DAU, MAU, API volume, app rankings — whatever is findable]
- **Weaknesses / perception gaps**: 
- **Strategic watch**: [what to watch in next 6-12 months]

[Repeat for each major player]

## Regional Divergence Map
[Where US/EU and CN/JP perceptions genuinely differ — this is often the most valuable section]

## The Gaps (What Nobody's Doing Well)
[Honest assessment of unmet needs surfaced from user complaints across all regions]

## Funding Snapshot
| Company | Total Raised | Last Round | Lead Investor | Valuation |
|---------|-------------|------------|---------------|-----------|
[table]

## Source Notes
[Brief list of key sources used, especially non-English ones]
```

---

## Step 5: Quality Check Before Delivering

Before writing the final output, verify:

- [ ] At least 3 Chinese-language sources were consulted (not just English coverage of Chinese companies)
- [ ] At least one Japanese or Korean source was found
- [ ] Every major player has a *spike* that is distinct from others
- [ ] The "Regional Divergence Map" section contains at least 2 genuine differences in perception
- [ ] Funding data is present for any company that has raised >$10M
- [ ] The "Gaps" section contains things not listed in any company's marketing

If any of these are missing, do additional targeted searches before finalizing.

---

## Output Format

- Deliver as a **downloadable `.md` file** using `create_file` → `present_files`
- Also display the TL;DR and Market Map sections inline in chat so the user gets immediate value
- File should be named: `[industry-slug]-market-landscape-[YYYY-MM].md`

---

## Notes on Chinese Source Research

When searching Chinese sources, use these query patterns:

| Goal | Query pattern |
|------|--------------|
| Company reputation | `[company] 评测 site:zhihu.com` |
| User complaints | `[company] 缺点 OR 问题 OR 吐槽` |
| Funding news | `[company] 融资 site:36kr.com` |
| Technical discussion | `[company] site:jiqizhixin.com` |
| Consumer sentiment | `[company] 好用吗 OR 怎么样` |
| Comparisons | `[companyA] vs [companyB] 对比` |

If direct Chinese searches are blocked or return limited results, search for English-language coverage that *cites* Chinese sources (e.g., "according to 36kr", "Zhihu users say").

---

## Tone and Framing

- Write like a sharp analyst who has *used* the products, not like a Wikipedia editor
- Be willing to say "Company X's spike is mostly marketing, real users prefer Y for Z"
- Avoid hedge words like "may", "could potentially", "seems to" when evidence exists
- Call out when a company is well-funded but has weak user perception (a common pattern)
- Flag when Chinese market perception is significantly ahead of Western awareness (this happens frequently in AI tooling)
