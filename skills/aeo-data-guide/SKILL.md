---
name: aeo-data-guide
description: How to think about AirOps AEO data — interpreting metrics, handling ambiguous asks, routing to the right agent.
auto-apply: true
disable-model-invocation: false
---

## The Data Model (Conceptual)

AirOps tracks how AI search engines talk about a brand. The core idea:

1. A brand has **prompts** — questions people ask AI (e.g. "What are the best AEO tools?")
2. Each prompt gets passed into multiple **AI providers** (ChatGPT, Gemini, Perplexity, etc.)
3. Each provider returns an **answer**
4. Each answer is checked: did it **mention** the brand? Did it **cite** (link to) the brand?
5. From this, we get metrics: mention rate, citation rate, share of voice, etc.

There's also a **page** side — how each URL on the brand's site performs across
AEO (AI citations), GSC (Google search), and GA4 (site analytics).

Everything hangs off a **Brand Kit**, which lives in a **Workspace**. You always
need to find the right brand kit first.

---

## Two Domains of Data

| Domain | What it covers | Agent |
|--------|---------------|-------|
| **AI search visibility** | Prompts, answers, mentions, citations, competitors, share of voice | **ai-search-analyst** |
| **Page performance** | Page-level AEO + GSC + GA4 metrics, smart filters, optimization opportunities | **page-analyst** |

Delegate to the right agent. If a question spans both (e.g. "which pages are losing
AI citations and also losing Google rankings?"), start with the page-analyst (it
has the combined view) and escalate to ai-search-analyst for deeper prompt/answer
drill-downs.

---

## Branded vs Non-Branded (Critical Guardrail)

Prompts have a `brand_mentioned` field. When the brand is in the question
("What is AirOps?"), AI will almost always mention it — inflating metrics.

**Default to non-branded (`brand_mentioned: false`) for all analysis.**

Only include branded when:
- User explicitly asks for branded vs non-branded comparison
- Analyzing brand-name recognition specifically

If metrics look suspiciously high, check whether branded prompts are included.

---

## Handling Ambiguous Requests

Users often ask vague things. Here's how to route them:

### "How are we doing?"
- Start with overall health: mention_rate, citation_rate, share_of_voice
- Break down by provider and topic to find the story
- Compare to benchmarks (5-15% mention rate for non-branded is good)
- Delegate to **ai-search-analyst**

### "What should we work on?"
- Check pages with smart filters: losing_ai_visibility, citation_rate_decline, almost_page_one
- Check prompts where brand is NOT mentioned but volume is high (opportunities)
- Delegate to **page-analyst** for page opportunities, **ai-search-analyst** for prompt gaps

### "How do we compare to competitors?"
- share_of_voice and citation_share by competitor dimension
- Drill into which topics competitors dominate
- Delegate to **ai-search-analyst**

### "Show me our best/worst performing content"
- Pages sorted by citation_rate or citations_count (best) or using smart filters (worst)
- Delegate to **page-analyst**

### "What's happening with [specific provider]?"
- Filter analytics to that provider
- Compare mention_rate and citation_rate vs other providers
- Delegate to **ai-search-analyst**

### When you don't have enough context — ASK:

- **No time period specified** → "What time range? Last 30 days, 90 days, or a specific period?"
- **No brand kit clarity** → List brand kits and ask which one
- **"Report" is vague** → "Do you want a summary in chat, a saved markdown file, or a dashboard in AirOps?"
- **Topic unclear** → List topics and ask which to focus on
- **"Performance" is broad** → "Are you asking about AI search visibility (mentions/citations) or page performance (traffic/rankings)?"

Don't guess. A quick clarifying question saves a wrong analysis.

---

## Reading AI Search Numbers

### Mentions first, citations second

**Mention rate is the primary metric for AI search visibility.** It tells you
whether AI is actually talking about the brand. If you're not mentioned, nothing
else matters — citations, sentiment, and position are all downstream of being
mentioned in the first place.

Citation rate is important but secondary — it measures whether AI links back to
your content. You can be mentioned without being cited (brand is known but content
isn't linked), but you generally won't be cited without being mentioned.

When reporting or analyzing:
- Lead with mention rate and share of voice
- Follow with citation rate as supporting context
- Don't fixate on citations alone — a brand that's mentioned everywhere but cited
  rarely has a different problem than one that's not mentioned at all

### Metric combinations that tell a story

| Pattern | What it means | Action |
|---------|--------------|--------|
| High mention, low citation | Brand is known but not linked to | Create more authoritative, linkable content so AI has something to cite |
| Low sentiment | AI talks about you negatively | Flag for messaging/PR review |
| Declining mention + stable citation | Losing mindshare but content still works | Brand awareness campaign needed |
| Declining citation + stable mention | Losing authority but still known | Content refresh — update outdated pages so AI trusts them again |

### Volume matters

`relative_volume_score` (0-1) tells you how popular a prompt is. A prompt with
0.9 volume where you're not mentioned is a much bigger opportunity than one with
0.1 volume. Always factor in volume when prioritizing.

---

## Reading Page Numbers

Page data combines three sources: AEO (AI citations), GSC (Google Search), and
GA4 (site analytics). The power is in reading them together — one source shows
what's happening, the others show why.

### Single-signal patterns

| Pattern | What it means | Action |
|---------|--------------|--------|
| Losing AI citations (`losing_ai_visibility`) | AI is dropping references to this page | Content is going stale for AI. Refresh with current data, stats, examples. Check what AI is citing instead. |
| Losing clicks, stable position (`losing_clicks`) | Ranking hasn't changed but fewer people click | Something else is eating clicks — likely AI overviews or featured snippets. Review meta title/description. Consider if AI is answering the query directly. |
| Rankings slipping (`rankings_slipping`) | Position declining over time | Classic SEO issue — competitors may have fresher/better content. Audit and refresh. |
| Almost page one (`almost_page_one`) | Ranking #11-20 | Quick win — small improvements could push to page 1. Strengthen content, add internal links, improve on-page SEO. |
| Citation rate decline, stable SEO (`citation_rate_decline`) | AI losing faith in this page while Google still ranks it | Early warning. AI updates faster than Google. If AI drops you, Google may follow. Refresh content now before both decline. |

### Multi-signal patterns (the real insights)

| AEO signal | GSC signal | GA4 signal | What's happening | What to do |
|------------|-----------|------------|-----------------|------------|
| Citations dropping | Clicks dropping | Traffic dropping | Everything is declining. This page is losing relevance across the board. | Major content refresh or rewrite. Check what competitors/citations are winning instead. |
| Citations dropping | Clicks stable | Traffic stable | AI is moving away from this page but Google still sends traffic. | The page works for traditional search but isn't structured for AI citation. Update to be more authoritative/quotable. |
| Citations stable | Clicks dropping | Traffic dropping | AI still cites you but Google traffic is falling. | Traditional SEO issue — position may be slipping, or AI overviews are taking clicks. Check position trend. |
| Citations rising | Clicks stable | Traffic rising | AI is sending you more traffic via citations. | This page is winning in AI search. Study what makes it work and replicate the pattern. |
| Citations stable | Clicks rising | Traffic rising | Google is sending more traffic, AI unchanged. | Traditional SEO win. Don't change what's working — focus AI optimization effort elsewhere. |
| No citations | High clicks | High traffic | Google loves this page but AI doesn't cite it. | Opportunity — optimize this high-traffic page to be more citable by AI (structured data, clear answers, authoritative framing). |

### How to navigate page analysis

1. **Start broad** — use smart filters to find pages that need attention
2. **Check the combination** — don't act on one signal alone. A page losing clicks but gaining citations is a different situation than one losing both.
3. **Drill into why** — use `get_page_prompts` to see which AI prompts cite the page. Are those prompts losing volume? Are competitors getting cited instead?
4. **Cross-reference** — if a page is losing AI citations, delegate to ai-search-analyst to check if it's a page-specific issue or a broader trend (maybe all pages on that topic are declining).
5. **Prioritize by impact** — pages with high traffic + declining signals need attention first. A page with 10 monthly visits losing citations is less urgent than one with 10,000.

---

## Refresh vs Create (Critical Guardrail)

When you find a prompt or keyword opportunity (high volume, low mention rate),
**always check if a page already exists before suggesting action.**

### The rule

1. Identify the keyword or topic from the prompt opportunity
2. Query `list_pages` — search by keyword, URL, or folder to see if the brand
   already has a page targeting it
3. **If a page exists** → recommend refreshing/optimizing that page. Don't create
   a competing page.
4. **If no page exists** → recommend creating new content for that keyword.

### Why this matters

Suggesting "create a blog post about X" when the brand already has a page about X
leads to duplicate content competing with itself. The existing page might just need
a refresh — updated stats, better structure for AI citation, stronger authority signals.

### How to check

- `list_pages` filtered by `primary_keyword` matching the prompt's keyword
- `list_pages` filtered by `url` containing relevant terms
- If unsure, search broadly by folder (e.g. `/blog/`) and scan for related content

### What to recommend

| Situation | Recommendation |
|-----------|---------------|
| Page exists, performing well | Leave it alone — focus effort elsewhere |
| Page exists, losing citations or traffic | Refresh: update content, add current data, optimize for AI |
| Page exists, no AI citations at all | Optimize: restructure for AI citability (clear answers, authoritative framing) |
| No page exists, high volume keyword | Create: new content targeting this keyword |
| No page exists, low volume keyword | Deprioritize unless strategically important |

---

## What NOT to Do

- **Don't include branded prompts by default** — inflates everything
- **Don't report raw numbers without context** — "12% mention rate" means nothing without the benchmark and trend
- **Don't analyze a single time snapshot** — always check the trend
- **Don't mix up mention and citation** — mention = named in the response, citation = linked to. Very different signals.
- **Don't compare across providers without noting the difference** — each AI has different behavior patterns
- **Don't act on one signal alone for pages** — always cross-reference AEO + GSC + GA4 before recommending action
- **Don't guess at metric definitions** — check the AirOps concepts resource if unsure
