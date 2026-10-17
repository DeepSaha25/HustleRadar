<!--
DEV POST SETTINGS

Title: A ₹7,000 discount can still be a bad deal — I built HustleRadar to audit the flip before you buy
Tags: devchallenge, hf26challenge, gemma, opensource

Set the title and tags in DEV's fields, then copy this entire file into the
DEV Markdown editor. This comment is invisible in the published article.
-->

*This is a submission for the [Hacktoberfest Open-Source AI Challenge Week 2: Side Hustle](https://dev.to/challenges/hacktoberfest-week2-2026-10-12).*

> **The challenge:** build something with open-source AI at its core that someone would actually pay for. Think small, specific and useful — then explain what open innovation made possible that a closed API would not.

# A ₹7,000 discount can still be a bad deal

I built **HustleRadar** because a crossed-out price is not the same thing as a profitable resale opportunity.

₹15,000 against a displayed reference price of ₹22,000 looks like an instant ₹7,000 saving. But a reseller still has to answer harder questions:

- Is the reference price meaningful, or was it inflated for the sale?
- What are other stores charging for the exact same variant today?
- What price could I realistically sell it for?
- What remains after marketplace fees, delivery, coupons and other costs?
- Is this a real opportunity, or just a convincing product page?

HustleRadar turns those questions into one evidence-backed workflow: **enter a product and an offer price, compare the market, calculate the exit margin, then ask an open-weight model to explain the result.**

![HustleRadar report showing a deal verdict, retailer prices, market median and resale margin](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/10-final-report.png)

*The report above is a labelled, reproducible demo scenario. It is included to show the product flow; it is not presented as a claim that those particular prices are live today.*

## The short version

- 🌐 **[Try the live application](https://hustleradar.onrender.com/)**
- 🎥 **[Watch the walkthrough on YouTube](https://youtu.be/BsQCG8vhWxs)**
- 🐙 **[Read the source on GitHub](https://github.com/DeepSaha25/HustleRadar)**
- ❤️ **[Check the deployed health endpoint](https://hustleradar.onrender.com/api/health)**
- 🧪 **24 automated tests pass**, including live/demo separation, variant matching and streaming behavior

The application is built for people who buy expensive inventory with their own money: electronics flippers, retail arbitrage beginners, freelance studios, photographers, video editors and small e-commerce operators.

The promise is deliberately narrow:

> **Do not buy because a product page says “discount”. Buy only after you can see the market, the fee-adjusted exit price and the evidence behind the recommendation.**

---

## What I Built

HustleRadar is an open-source **market evidence and arbitrage decision tool**. It is not a general-purpose shopping chatbot and it does not pretend that a language model can see the web by itself.

The user enters:

1. A product name or product URL
2. The price they would actually pay
3. An optional original/reference price
4. An optional live or demo mode

The product then runs a complete decision loop:

1. **Collect evidence** from shopping results, selected retailers and dated context.
2. **Normalize the product identity** so accessories, refurbished listings and wrong variants do not pollute the comparison.
3. **Calculate the market picture**: lowest observed price, median price, source coverage and fee-adjusted resale margin.
4. **Show the evidence** with retailer names, links, prices, timestamps and coverage warnings.
5. **Use Google Gemma 2** to turn those structured facts into a short, practical explanation for a buyer or side hustler.
6. **Fall back safely** to a deterministic evidence-backed rationale if the model is disabled or unavailable.

### Start with a product, not a conversation

The input screen is intentionally small. A reseller should be able to paste a product, type the real buy price and start the check without writing a prompt.

![HustleRadar input screen with Sony WH-1000XM5, a ₹15,000 offer price and a ₹22,000 reference price](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/12-input-screen.png)

The interface also makes the boundary visible: **live data, multiple retailers, Gemma 2 AI and local inference availability** are capabilities of the system, not a guarantee that every provider will always respond.

### Compare the market instead of trusting the banner

HustleRadar compares the product across multiple stores and keeps the source attached to each number. The point is not to produce the biggest discount percentage; it is to find the price floor and the price range a real buyer may encounter.

![HustleRadar price comparison view showing prices by store](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/02-prices-by-store.png)

### Make the resale calculation explicit

The report separates the numbers that are usually mixed together on a product page:

- sourcing price — what the user pays
- market floor — the lowest comparable listing found
- market median — a less fragile view of the market than one outlier
- estimated resale fee — currently modelled as an 8% marketplace fee
- net resale margin — what remains after the fee and sourcing cost

![HustleRadar arbitrage report showing resale margin and flip feasibility](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/03-resale-arbitrage-margin.png)

The simplified calculation is:

```text
estimated net profit = expected resale price × (1 − marketplace fee) − sourcing price
```

Delivery, coupons and bank discounts can be modelled in the scenario calculator rather than hidden inside a single optimistic percentage.

![HustleRadar scenario calculator for bank discounts, delivery fees and coupons](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/05-scenario-calculator.png)

### Show why the recommendation exists

The most important part of an AI recommendation is not the adjective “good” or “bad”. It is whether a person can inspect the path from source evidence to conclusion.

The evidence view exposes the coverage and provenance behind the report:

![HustleRadar evidence coverage view showing source status and market evidence](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/04-evidence-coverage.png)

That makes the app useful even when the answer is **“insufficient data”**. A missing market observation is safer than a confident-looking number invented to complete the card.

### Protect the comparison from near matches

“Sony WH-1000XM5” should not be compared with WH-1000XM4, replacement ear pads, a protective case or a refurbished unit. HustleRadar applies product and condition checks before prices enter the calculation.

![HustleRadar variant selection screen for narrowing the exact product match](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/06-variant-selection.png)

This is a small detail with a large effect: a ₹999 accessory can make an apparently excellent margin if it is allowed into a ₹25,000 headphone comparison.

---

## Demo

### Live mode, demo mode and honest failure

You can try the application at **[hustleradar.onrender.com](https://hustleradar.onrender.com/)**. The walkthrough is on **[YouTube](https://youtu.be/BsQCG8vhWxs)**.

The application has two explicit evidence modes:

- **Live mode** calls the configured search providers and reports the provider status it actually receives.
- **Demo mode** loads clearly labelled fixtures so the complete UI can be demonstrated without an API key, quota or unstable retailer pages.

The screenshots in this article use the stable demo scenario where indicated. That is intentional: the article should remain reproducible instead of quietly turning a quota failure into a fake live result.

If a live provider fails, the report remains `live`, has no fabricated demo sellers, and can return `insufficient_data`. Demo fixtures are only loaded when the caller explicitly requests `mode: "demo"`.

![Render dashboard showing the live HustleRadar service and the deployed commit](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/13-render-deployment.png)

![Render health endpoint and deployment verification for HustleRadar](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/07-render-health.png)

---

## Code

### Repository

The complete source is available here:

> **[github.com/DeepSaha25/HustleRadar](https://github.com/DeepSaha25/HustleRadar)**

The most relevant implementation files are:

- [`evidenceService.ts`](https://github.com/DeepSaha25/HustleRadar/blob/main/apps/server/src/services/evidenceService.ts) — live/demo evidence collection, caching and provider status
- [`llmClient.ts`](https://github.com/DeepSaha25/HustleRadar/blob/main/apps/server/src/services/llmClient.ts) — Gemma routing and deterministic fallback
- [`validation.ts`](https://github.com/DeepSaha25/HustleRadar/blob/main/apps/server/src/services/validation.ts) — product identity, currency and listing guards
- [`server.test.js`](https://github.com/DeepSaha25/HustleRadar/blob/main/apps/server/test/server.test.js) — offline regression tests
- [`render.yaml`](https://github.com/DeepSaha25/HustleRadar/blob/main/render.yaml) — Render deployment blueprint

### Evidence collection stays separate from the verdict

The backend collects three evidence streams concurrently. The model does not replace this work:

```ts
const [shopping, retailers, context] = await Promise.all([
  run("shopping", () => searchShoppingDetailed(query, signal, forceRefresh)),
  run("retailers", () => searchMajorRetailersDetailed(query, signal, forceRefresh)),
  run("context", () => searchContextDetailed(query, signal)),
]);

return {
  query,
  mode: "live",
  shopping,
  retailers,
  context,
};
```

Each batch retains its status. A successful empty search is still an observation; a provider error is not converted into a successful result.

### Demo fixtures require an explicit mode

The important safety boundary is small but deliberate:

```ts
if (mode === "demo") {
  const evidence = fixture(query);
  setEvidence(cacheKey, evidence);
  return { evidence, fromCache: false };
}

const evidence = await collectLive(query, signal, progress, forceRefresh);
```

This prevents a failed live search from being “helpfully” filled with demo data. It also makes cached evidence mode-specific, so a demo report cannot leak into a live request.

### Gemma receives facts, not a blank prompt

The prompt sent to Gemma contains the normalized product, verdict, claimed price, reference price, market median, lowest comparable listing and calculated arbitrage result:

```ts
const prompt = `You are HustleRadar's open-source deal auditor and
retail arbitrage advisor powered by Google Gemma 2.

Evaluate this product deal strictly using the verified market evidence below.
Do NOT hallucinate prices or retailers.

Product: ${params.productTitle}
Verdict: ${params.verdict} (Confidence: ${params.confidence})
Claimed Price: ${formatPrice(params.claimedPrice)}
Market Median Price: ${formatPrice(params.medianPrice)}
Lowest Listing: ${params.cheapestSeller?.name}
Arbitrage Net Profit Potential: ${formatPrice(params.arbitrage?.netProfit)}
`;
```

The model is asked to explain two decisions:

1. Is the advertised discount genuine or inflated?
2. Is there viable resale margin after the estimated marketplace fee?

That is a much narrower and more auditable job than asking a chatbot to browse the internet and guess.

### A model failure should not erase the arithmetic

Gemma can be disabled with `ENABLE_LLM=false`. If Ollama or an optional open-model endpoint is unavailable, HustleRadar returns a deterministic rationale generated from the same structured evidence and calculations.

The model can improve the explanation. It does not own the underlying prices, fee calculation or product-match decision.

## How I Built It

HustleRadar is built around a simple architectural rule:

> **Use open-weight AI for synthesis, but make the source evidence and the important arithmetic inspectable without trusting the model.**

### The stack

| Layer | Technology | Responsibility |
| --- | --- | --- |
| Interface | React 18, TypeScript, Vite, Tailwind CSS | Product input, streamed progress and decision report |
| API | Node.js, Express and TypeScript | Request validation, orchestration and report assembly |
| Search grounding | SerpApi shopping and retailer search | Current offers, retailer evidence and dated context |
| Product guardrails | Deterministic matching and INR parsing | Reject wrong models, accessories, refurbished items and non-INR prices |
| Decision engine | Deterministic median, fee and scenario calculations | Compute the numbers before language generation |
| Open model | Google Gemma 2 through Ollama or supported endpoints | Explain the evidence for a buyer or reseller |
| Transport | NDJSON streaming | Show shopping, retailer and context progress as it happens |
| Deployment | Render Blueprint | Serve the frontend and Node API from reproducible configuration |
| Verification | Node test runner and production builds | Protect the important boundaries offline |

### The architecture

```text
Browser
  React + TypeScript + Vite
      │
      │ POST /api/check-deal/stream
      ▼
Node + Express API
  ├─ query and URL validation
  ├─ SerpApi shopping search
  ├─ retailer-specific search
  ├─ dated context search
  ├─ product/variant/condition filters
  ├─ median + fee + scenario calculator
  ├─ Gemma 2 rationale (optional)
  └─ deterministic rationale fallback
      │
      ▼
Report
  ├─ verdict and confidence
  ├─ price comparison matrix
  ├─ arbitrage margin
  ├─ evidence receipts
  └─ warnings and provider coverage
```

### 1. Ground the model with multiple search paths

One search query is not enough for a resale decision. HustleRadar uses separate paths for:

- **Shopping results** — structured offers and merchant prices.
- **Major retailer searches** — direct retailer pages that an aggregator may miss.
- **Context search** — dated mentions that help distinguish a current market price from a temporary marketing reference.

The result is stored as evidence first. The application can then show the source, status and timestamp instead of reducing everything to an unsupported answer such as “great deal”.

### 2. Normalize the product before calculating a margin

The hardest part of price comparison is often not arithmetic. It is deciding whether two listings describe the same thing.

The validation layer rejects or flags:

- the wrong model number, such as WH-1000XM4 for a WH-1000XM5 query;
- accessories such as cases, cables and replacement ear pads;
- refurbished or used listings when the requested product is new;
- mismatched storage, RAM, size or colour variants when those attributes matter;
- prices that are not valid Indian rupee values.

This is why a cheap accessory cannot accidentally become the “lowest seller” for an expensive product.

### 3. Calculate the decision before asking Gemma to write it

The recommendation engine calculates the market median, the lowest matching seller and the fee-adjusted resale outlook first. This creates a stable baseline that can be checked in tests and displayed in the UI.

The language model then answers a smaller question: **how should these verified facts be explained to a person deciding whether to buy?**

That separation is the product's main design choice. It gives a small open model a useful job without making it responsible for discovering or inventing the market.

### 4. Keep the model replaceable

The verified local model run used **`gemma2:9b` through Ollama** at `http://127.0.0.1:11434`. The code also supports configured open-model endpoints, while the deterministic path remains available when the model is disabled.

![Local Gemma 2 demonstration with the evidence-backed HustleRadar analysis](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/08-local-gemma-demo.png)

The model name, endpoint and enable/disable behavior come from environment variables rather than hard-coded credentials. Secrets stay in `.env` or deployment configuration and are not committed to the repository.

### 5. Stream progress instead of showing a frozen spinner

Search takes longer than a local calculation. The API emits NDJSON progress events while it checks shopping results, retailers, context and analysis. The frontend can show which evidence path is running, which one finished and where a provider returned an error.

That small interaction detail matters for trust: the user can see that the report is being assembled rather than waiting for an opaque “AI answer”.

---

## The bugs and design decisions that mattered

The most important work was not making the happy path look impressive. It was making the failure paths honest.

### Bug 1: a provider failure silently looked like a successful scan

An earlier implementation could fall back to demo fixtures when a live provider failed. That made the UI look complete, but it destroyed the meaning of “live”. A reseller could believe the report came from current market evidence when it did not.

The fix was to make the mode part of the evidence contract:

- live requests stay live;
- provider errors remain errors;
- demo fixtures load only after an explicit demo request;
- partial failures are not cached as healthy full reports;
- the UI can show `insufficient_data` instead of inventing confidence.

This is also why the demo screenshots in this article are labelled rather than presented as live prices.

### Bug 2: the cheapest listing was not always the right product

Search engines return useful results mixed with noise. A product query can bring back a case, a replacement part, a previous model or a refurbished unit. Price aggregation without identity checks rewards the wrong listing.

The validation layer now checks model identity, condition, accessory language, variant attributes and currency before a listing reaches the margin calculation.

### Design decision: confidence should describe evidence, not model fluency

A polished Gemma paragraph is not evidence. HustleRadar's confidence and warnings are based on source coverage and matching results. The model's role is to explain the report, not to upgrade weak evidence into a strong recommendation.

### What HustleRadar does not promise

- It does not guarantee that a reseller will sell an item at the market median.
- It does not guarantee that a retailer has stock after the scan.
- It does not treat a displayed MRP as historical truth.
- It does not silently convert a provider outage into a demo result.
- It does not claim that open-weight AI removes every cost: live search providers, hosting and marketplace fees still exist.

Those limits are part of the product. A useful side-hustle tool should help someone avoid a bad purchase, not create a new kind of overconfidence.

## Why Does Open Innovation Matter?

The Week 2 prompt asks what an open-based approach made possible that a closed API would not. For HustleRadar, the answer is not simply “the model was free”. It changed the boundaries of the product.

### 1. Local, private deal analysis

A reseller's watchlist can reveal the products, suppliers and niches they are trying to find. With Ollama, the structured report can be sent to a local Gemma model instead of a hosted general-purpose model.

The local path keeps the analysis on the user's machine and makes the privacy trade-off explicit. The live market search is still an external dependency when enabled, but the language-generation step does not have to leave the laptop.

### 2. Lower marginal cost for a small business

The intended user is not an enterprise with an AI budget. They may be checking a handful of products after work or auditing inventory before committing personal savings.

Running `gemma2:9b` locally has no per-token model charge. That does **not** make the whole stack free — SerpApi, hosting and resale fees still matter — but it removes one recurring cost and makes experimentation practical for a small operator.

### 3. Model choice without rewriting the product

The application owns the prompt contract and the evidence schema. It can use local Ollama, a configured open-model endpoint or the deterministic rationale path without changing the report UI.

That makes the model replaceable. A user can trade speed, memory, privacy and quality without giving up the product workflow or their stored reasoning about fees and evidence.

### 4. Inspectable behavior at the boundary where it matters

Closed APIs often make it difficult to reproduce exactly why a recommendation changed. Here, the key boundary is in the repository:

```text
search → normalize → calculate → explain
```

The first three stages are visible and testable. The open model receives a constrained input and can be disabled. That is a more useful form of AI safety for this product than claiming that a prompt makes a model incapable of error.

### 5. A path to niche improvements

An open model can eventually be adapted to the language of a particular resale community, a regional marketplace or a user's own historical sales data. The product can improve its prompts, filters and model choice without waiting for a closed provider to expose the right control.

Open innovation made it possible to build a tool where the model is powerful enough to help, but not powerful enough to hide the evidence from the person risking their money.

---

## Verification and deployment

The repository includes an offline test command:

```bash
npm test
```

The current suite has **24 passing tests** covering:

- query sanitization and INR-only price parsing;
- accessory, refurbished and wrong-model rejection;
- explicit demo mode and cache separation;
- honest live provider failure;
- streamed NDJSON progress and results;
- deterministic rationale when Gemma is disabled;
- rejection of ambiguous short product URLs.

![HustleRadar test suite showing 24 passing tests](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/09-tests-passing.png)

The production build also completes for the Node API and React frontend. Render is configured through [`render.yaml`](https://github.com/DeepSaha25/HustleRadar/blob/main/render.yaml), which defines the web frontend and Node API services, their build commands, the API connection and the production environment variables.

The deployed health check is available at:

```text
https://hustleradar.onrender.com/api/health
```

---

## Who would pay for this?

HustleRadar is intentionally aimed at a narrow, money-sensitive workflow.

### Retail arbitrage beginners

Someone who buys a ₹15,000–₹50,000 product to resell cannot treat a “40% off” badge as a business case. A small fee or a lower competing listing can erase the profit before the product even ships.

### Electronics flippers

Headphones, phones, cameras, laptops and accessories have frequent model and condition mismatches. A like-for-like comparison is more useful than a generic shopping search.

### Freelance studios and independent professionals

The user may not resell every purchase, but they still need to know whether a camera, monitor, laptop or audio device is priced fairly before spending a project budget.

### Small e-commerce operators

The scenario calculator helps model a real buying decision instead of forcing the user to do fee and discount arithmetic in a spreadsheet after the fact.

This is the kind of product I would expect a serious user to pay for if it consistently prevented even one bad inventory purchase. I am keeping the claim honest: the current article does not invent a paying customer or a testimonial that has not been verified.

If you buy or flip electronics, I would like to know which fee, marketplace or product variant the current version gets wrong first.

---

## Prize Categories

I am entering only categories that the repository and deployed application can substantiate:

### 🏆 Theme: Side Hustle

HustleRadar is built around a concrete side-hustle decision: **should I buy this item if I want a realistic resale margin?** It targets a small, specific user group and turns market research, product matching and fee arithmetic into a repeatable workflow.

### 🤖 Best Use of Gemma

HustleRadar uses Google's open-weight **Gemma 2** as the deal auditor and resale advisor. The verified local path runs `gemma2:9b` through Ollama. The model receives structured, normalized market evidence and explains deal authenticity and fee-adjusted resale potential. It can be disabled, replaced or run locally without changing the deterministic report engine.

### 🔎 Best Use of SerpApi

SerpApi provides the fresh market grounding: shopping results, direct retailer searches and dated context. The application preserves provider status and source links, filters the results before calculation and exposes coverage warnings instead of presenting unsupported numbers as facts.

### 🚀 Best Use of Render

The live application is deployed at [hustleradar.onrender.com](https://hustleradar.onrender.com/). The repository includes a Render Blueprint that builds the React frontend and Node API from the same project, with production configuration kept outside the repository.

I am not claiming a `.tech`, DigitalOcean, ElevenLabs, Tinker, Backboard, Sentry or other partner category because this project does not have verified use of those services.

---

## Final note

A good deal is not a crossed-out price.

It is a price with:

- a matching product;
- more than one source;
- a realistic exit price;
- fees already subtracted;
- evidence a buyer can inspect; and
- enough uncertainty shown honestly to make a better decision.

That is what I wanted HustleRadar to make possible: **an open-source AI side-hustle tool that helps people keep their margin before they commit their money.**

If you try it, tell me what you would add next: a marketplace fee, a regional retailer, a better variant rule or a different open model.

Built for the [Hacktoberfest Open-Source AI Challenge Week 2](https://dev.to/challenges/hacktoberfest-week2-2026-10-12).
