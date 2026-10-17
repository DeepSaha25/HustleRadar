*This is a submission for the [Hacktoberfest Open-Source AI Challenge Week 2: Side Hustle](https://dev.to/challenges/hacktoberfest-week2-2026-10-12).*

> **The challenge:** build something with open-source AI at its core that someone would actually pay for. Think small, specific and useful — then explain what open innovation made possible that a closed API would not.

## A ₹7,000 discount can still be a bad deal

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
4. A server-side evidence mode: live for the investigator flow, or explicit demo fixtures for reproducible testing and previews

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

The API has two explicit evidence modes:

- **Live mode** calls the configured search providers and reports the provider status it actually receives.
- **Demo mode** loads clearly labelled fixtures so the complete UI can be demonstrated without an API key, quota or unstable retailer pages.

The screenshots in this article use the stable demo scenario where indicated. That is intentional: the article should remain reproducible instead of quietly turning a quota failure into a fake live result.

If a live provider fails, the report remains `live`, has no fabricated demo sellers, and can return `insufficient_data`. Demo fixtures are only loaded when the caller explicitly requests `mode: "demo"`.

The backend supports both modes, while the current investigator flow submits live scans by default. The landing page's demo visuals and the offline tests use clearly labelled fixtures; the live search screen never silently changes a failed provider response into demo evidence.

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

### Deterministic arbitrage math comes before language generation

The margin is calculated in TypeScript before Gemma is called. The current product models an 8% resale-platform fee and classifies the opportunity using both percentage margin and absolute net profit:

```ts
const resaleMedianPrice = medianPrice;
const grossMargin = resaleMedianPrice - buyPrice;
const marginPercent = Math.round((grossMargin / buyPrice) * 100);
const platformFeeEst = Math.round(resaleMedianPrice * 0.08);
const netProfit = grossMargin - platformFeeEst;

if (grossMargin > 0 && netProfit > 0) {
  opportunityScore =
    marginPercent >= 20 && netProfit >= 2000 ? "HIGH" :
    marginPercent >= 10 || netProfit >= 800 ? "MODERATE" : "LOW";
} else {
  opportunityScore = "UNPROFITABLE";
}
```

This is the boundary that makes the product useful for a money-sensitive decision. Gemma can make the result easier to understand, but it cannot change the arithmetic by writing a more persuasive paragraph.

### Gemma routing is optional, replaceable and testable

The local model path is deliberately configuration-driven:

```ts
if (/^(?:0|false|off|no)$/i.test(process.env.ENABLE_LLM?.trim() ?? "")) {
  return null;
}

const gemmaUrl =
  process.env.GEMMA_API_URL ||
  (process.env.OLLAMA_BASE_URL
    ? `${process.env.OLLAMA_BASE_URL}/api/generate`
    : "http://127.0.0.1:11434/api/generate");

const model =
  process.env.GEMMA_MODEL_NAME ||
  process.env.OPEN_MODEL_NAME ||
  "gemma2:9b";
```

The request uses `stream: false`; the product streams evidence progress, not Gemma tokens. If local Ollama is unavailable, the implementation can try configured open-model endpoints and finally returns the deterministic rationale. The offline test suite disables LLM calls and verifies that the result still contains a useful rationale.

### Three SerpApi paths, one evidence contract

HustleRadar does not treat a single search response as the market. It runs three evidence paths concurrently:

```ts
const [shopping, retailers, context] = await Promise.all([
  run("shopping", () => searchShoppingDetailed(query, signal, forceRefresh)),
  run("retailers", () => searchMajorRetailersDetailed(query, signal, forceRefresh)),
  run("context", () => searchContextDetailed(query, signal)),
]);
```

Those paths mean:

- `google_shopping` for structured offers;
- retailer-restricted Google searches for direct store coverage;
- dated Google context for price mentions and historical signals.

Every request is server-side, uses `gl=in` and `hl=en`, applies a bounded timeout and preserves a provider status. A SerpApi HTTP 200 containing an error, quota response or invalid payload is not classified as a successful empty scan.

### A listing must earn its way into the median

Before a seller can influence the report, it passes product identity, condition, variant, currency, URL and availability checks:

```ts
const assessment = assessListing(query, item.title);
if (!assessment.accepted) {
  excludedCount++;
  continue;
}

const variant = listingVariant(query, item.title);
if (!variant.complete || !plausibleProductPrice(query, item.price)) {
  excludedCount++;
  continue;
}
```

The same layer rejects accessories, books, replacement parts, refurbished mismatches, foreign currencies and wrong RAM/storage/screen configurations. If multiple configurations remain, HustleRadar stops and asks the user to select one instead of mixing them into a false median.

### The browser receives real progress events

The stream endpoint writes one NDJSON object per line and sends a final result only after deterministic analysis is complete:

```ts
res.setHeader("Content-Type", "application/x-ndjson; charset=utf-8");

const write = (payload: unknown): boolean => {
  if (!connected || res.writableEnded || res.destroyed) return false;
  res.write(`${JSON.stringify(payload)}\n`);
  return true;
};

const result = await execute(req.body, controller.signal, (event) => {
  write(event);
});

write({ type: "result", data: result });
res.end();
```

The React client reads the response body incrementally. That is why the loading screen can show shopping, retailer, context and analysis stages without using a fake timer. Disconnecting the browser aborts the provider work through an `AbortController`.

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

### 6. SerpApi is treated as a provider, not as truth

The request adapter adds the Indian market and English-language parameters on the server, bounds the request time and rejects provider payloads that contain an error even when the HTTP status is 200:

```ts
const response = await axios.get("https://serpapi.com/search.json", {
  params: { ...params, gl: "in", hl: "en", api_key: apiKey },
  timeout: providerTimeout(),
  signal,
  maxContentLength: 2_000_000,
  maxRedirects: 0,
});

const data = record(response.data);
if (!Object.keys(data).length || data.error || record(data.search_metadata).status === "Error") {
  throw new Error("provider-response-error");
}
```

Shopping rows must contain a real allowlisted retailer product URL and a usable INR price. Foreign currency, EMI/monthly prices, “from” ranges and ambiguous promotional text are excluded instead of being treated as the checkout price.

### 7. Direct retailer verification closes the search loop

For retailer-restricted results, the backend does not blindly trust the search snippet. It discovers candidate pages, verifies them on the retailer domain and keeps the source class visible:

```ts
const relevant = parsed.items.filter((item) =>
  retailerForUrl(item.link)?.domain === store.domain &&
  discoveryListingMatches(query, item.title),
);

const candidates = [...new Map(relevant.map((item) => [item.link, item])).values()].slice(0, 3);
const verified = await mapConcurrent(
  candidates,
  2,
  (item) => verifyRetailerListing(item, query, signal, forceRefresh),
);
```

The UI can distinguish “Read from the store page”, “Found on Google Shopping”, “Found in Google search” and “Demo price”. That makes SerpApi a grounding layer with receipts rather than an opaque source of numbers.

### 8. Render runs a two-service deployment

The selected Render category is supported by a checked-in Blueprint, not just a badge:

```yaml
services:
  - type: web
    name: hustleradar-web
    env: static
    buildCommand: npm install --include=dev && npm run build --workspace=apps/web
    staticPublishPath: ./apps/web/dist

  - type: web
    name: hustleradar-api
    env: node
    plan: starter
    buildCommand: npm install --include=dev && npm run build --workspace=apps/server
    startCommand: npm run start --workspace=apps/server
```

The frontend receives the API host through a Render service reference. SerpApi and Gemma keys are `sync: false`, so the credentials are configured in Render rather than committed to the repository. The health endpoint reports service status, provider configuration, optional LLM configuration, analysis version and timestamp.

Render hosts the application and API. It is not described here as the place where the local Ollama model runs; the verified local model path is `gemma2:9b` through Ollama on the developer machine.

### 9. The reproducibility boundary is explicit

The test suite is intentionally offline. Provider calls are mocked or disabled, so the tests prove the adapter contracts and failure behavior without pretending to prove an external quota or model endpoint. The current checks are:

```bash
npm test   # build server, then run the 24-test Node suite
npm run build
```

The repository includes `package-lock.json` files and the Render Blueprint. The article does not claim that the repo is a Docker image or that the test suite makes a live SerpApi request; it claims the narrower, verifiable thing: the core decision boundaries can be rebuilt and checked without network access.

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

The suite is designed to be honest about what it proves. SerpApi calls are mocked or disabled, and Gemma is disabled in the offline server tests. That means the tests verify the application’s adapters, validation rules, fallback behavior and streaming contract; they do not pretend to prove a live provider quota or a remote model endpoint.

![HustleRadar test suite showing 24 passing tests](https://cdn.jsdelivr.net/gh/DeepSaha25/HustleRadar@main/dev-ss/09-tests-passing.png)

The production build also completes for the Node API and React frontend. The build currently emits only non-blocking Vite warnings about future config-loader behavior and bundle size. Render is configured through [`render.yaml`](https://github.com/DeepSaha25/HustleRadar/blob/main/render.yaml), which defines the web frontend and Node API services, their build commands, the API connection and the production environment variables.

The deployed health check is available at:

```text
https://hustleradar.onrender.com/api/health
```

That endpoint is a deployment health/configuration check: it reports service status, whether SerpApi is configured, whether optional LLM configuration is present, the analysis version and a timestamp. It is not presented as proof that a live SerpApi request or a Gemma generation succeeded at that exact moment.

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

HustleRadar uses Google's open-weight **Gemma 2** as a bounded deal auditor and resale advisor. The verified local path runs `gemma2:9b` through Ollama at `http://127.0.0.1:11434`. Gemma receives a structured prompt containing the normalized product, deterministic verdict, confidence, claimed price, market median, lowest comparable listing and fee-adjusted arbitrage result.

The important technical choice is the order of operations:

```text
SerpApi evidence → product guards → deterministic verdict/margin → Gemma explanation
```

Gemma is not asked to discover prices or invent retailer names. It is instructed to stay inside the retained evidence. `ENABLE_LLM=false` bypasses the model, and the deterministic rationale remains available if Ollama or another configured open-model endpoint is unavailable. That makes the open model useful without making it the single point of correctness.

Evidence in the repository:

- `apps/server/src/services/llmClient.ts` — Gemma prompt, Ollama routing, optional provider paths and deterministic fallback;
- `apps/server/src/routes/checkDeal.ts` — deterministic verdict and arbitrage calculation before rationale generation;
- `apps/server/test/server.test.js` — offline test proving disabled Gemma causes zero network calls and still returns a rationale.

### 🔎 Best Use of SerpApi

SerpApi is the fresh-market grounding layer. HustleRadar uses three search paths: `google_shopping` for offers, retailer-restricted Google searches for store coverage and dated Google context for price mentions. They run concurrently, preserve provider status and feed a validation layer before any number reaches the median or margin calculation.

The integration is more than a search-box call:

- Google Shopping rows require an actual allowlisted retailer product URL and a usable INR price;
- foreign currencies, EMI/monthly prices, “from” ranges and ambiguous promotional text are excluded;
- retailer-restricted candidates are verified against the retailer page where possible;
- source labels distinguish retailer-page, Google Shopping, search-index and demo prices;
- coverage warnings remain visible when a store fails or has no matching configuration;
- a provider error never becomes a successful empty scan or demo evidence.

Relevant implementation:

- `apps/server/src/services/serpapiClient.ts` — request parameters, timeouts, Shopping parsing, retailer search, context search and response validation;
- `apps/server/src/services/retailerPageClient.ts` — direct page verification and same-domain redirect rules;
- `apps/server/src/services/retailers.ts` — domain allowlist and brand/region-aware retailer selection;
- `apps/server/src/services/evidenceService.ts` — concurrent evidence orchestration, cache mode separation and provider status.

### 🚀 Best Use of Render

The live application is deployed at [hustleradar.onrender.com](https://hustleradar.onrender.com/). The repository includes a Render Blueprint with two services: a static Vite/React frontend and a Node/Express API. The frontend gets the API host from a Render service reference, while `SERPAPI_KEY` and `GEMMA_API_KEY` are marked `sync: false` and stay in deployment configuration.

The Render-specific implementation is visible and reviewable:

```yaml
- type: web
  name: hustleradar-web
  env: static
  staticPublishPath: ./apps/web/dist

- type: web
  name: hustleradar-api
  env: node
  plan: starter
  startCommand: npm run start --workspace=apps/server
```

Render hosts the web application and API. It is not described as hosting the developer’s local Ollama process; the local Gemma path and the deployed service configuration are kept distinct.

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
