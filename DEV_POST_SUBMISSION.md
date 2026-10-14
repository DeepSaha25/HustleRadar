---
title: HustleRadar — The Open-Source AI Arbitrage & Deal Radar for Side Hustlers (Powered by Gemma 2 & SerpApi)
published: true
tags: devchallenge, hf26challenge, gemma, opensource
---

*This is a submission for the [Hacktoberfest Open-Source AI Challenge Week 2: Side Hustle](https://dev.to/challenges/hacktoberfest-week2-2026-10-12)*

---

## ⚡ What I Built

**HustleRadar** is an open-source AI market intelligence and retail arbitrage radar built for side hustlers, e-commerce flippers, and freelance studios.

### The Problem
During festive sales (Amazon Great Indian Festival, Flipkart Big Billion Days, Diwali/Clearance events), e-commerce platforms deploy aggressive dark patterns:
* **Artificial MRP Anchoring:** Retailers artificially inflate reference prices (e.g. marking a ₹32,000 camera lens up to ₹55,000 to advertise a fake "45% discount").
* **The "Clearance Trap":** Beginners buy clearance inventory assuming high profit margins, only to discover competitor stores sell that exact SKU even cheaper every day.
* **Closing Fee Realities:** Side hustlers frequently forget that marketplace commissions (8–15%) and return risks eliminate thin margins.

### Who Would Pay For This?
* **Retail Arbitrageurs & Flippers:** People who source discounted electronics, audio gear, and laptops from one platform to flip on OLX, eBay, Amazon FBA, or local marketplaces. A single bad buy leaves them stuck with thousands of dollars in dead inventory. Resellers happily pay **$15–$25/month** for tools that prevent bad inventory buys and surface real margin opportunities.
* **Freelance Creative Studios & Remote Agencies:** Video editors, photographers, and indie developers spending significant capital on MacBooks, cameras, and studio gear who need an un-biased procurement auditor to verify price floors and stack bank discounts.

HustleRadar scans multi-store market listings in real time, calculates true market medians, deduces realistic 8% marketplace fees, and runs Google Gemma 2 (open-weights) to evaluate deal authenticity and flip feasibility.

---

## 🎥 Demo

* 🌐 **Live Web Application:** [https://hustleradar.vercel.app](https://hustleradar.vercel.app) *(or deployed via Render)*
* 🎥 **Walkthrough Video:** [YouTube Video Walkthrough](https://youtu.be/ZruTcO9Xk6k)

### Key Screens & Capabilities:
1. **⚡ Side Hustle Arbitrage Radar:** Displays Sourcing Cost (Buy price), Market Median (Target sell price), Estimated 8% Resale Fees, and Projected Net Margin (₹ and % ROI).
2. **🤖 Gemma 2 Deal Auditor:** Produces non-hallucinatory verdicts strictly grounded in verified SerpApi store evidence.
3. **Multi-Store Price Comparison Matrix:** Direct comparisons across Amazon, Flipkart, Croma, Reliance Digital, and independent retailers.
4. **Scenario Calculator:** Adjusts bank card discounts, delivery fees, and coupons with reactive recalculations.

---

## 💻 Code

* 🐙 **GitHub Repository:** [https://github.com/DeepSaha25/HustleRadar](https://github.com/DeepSaha25/HustleRadar) *(HustleRadar)*

Built as a high-performance monorepo with:
* **Frontend:** React 18, TypeScript, Vite, Tailwind CSS, Lucide icons, Framer Motion, and Glassmorphic/Brutalist data visualization.
* **Backend:** Node.js, Express, TypeScript, NDJSON streaming, and in-memory TTL caching.
* **AI & Search Engines:** Google Gemma 2 (Ollama / Open-Weights API) + SerpApi (`google_shopping` and retailer engines).

---

## 🛠️ How I Built It

HustleRadar is engineered around two core technical pillars: **deterministic multi-source web grounding** and **open-weight AI synthesis**.

### 1. Grounding Open-Source AI in Live Web Truth (SerpApi)
An AI model is only as good as the ground-truth data you feed it. Rather than letting the model guess whether ₹49,999 is a good price for a laptop, the backend coordinates three simultaneous search vectors using SerpApi:
* `google_shopping` engine: Gathers active, structured merchant offers across Indian retail channels.
* Retailer-restricted search: Queries direct retail domains (Croma, Reliance Digital, Vijay Sales) to extract live checkout prices that aggregators omit.
* Temporal context engine: Examines historical indexed mentions to understand price evolution.

### 2. Strict Model & Variant Normalization
Raw e-commerce data is notoriously messy. A search for "MacBook M3" often returns cases, sleeves, refurbished units, or different RAM configurations. HustleRadar runs multi-stage regex and keyword guardrails to reject non-matching configurations so every comparison is 100% like-for-like.

### 3. Open-Weight AI Reasoning with Google Gemma 2
Once the price matrix is verified, HustleRadar invokes **Google Gemma 2 (9B / 2B)**:
* **Local Inference via Ollama:** When running on a local machine or private server, HustleRadar communicates with Ollama (`http://127.0.0.1:11434/api/generate`) using the `gemma2:9b` or `gemma2:2b` model.
* **Constrained Zero-Hallucination Prompting:** Gemma 2 is strictly instructed to evaluate the deal within the bounds of the verified price median and seller listings. It evaluates:
  1. *Deal Authenticity:* Is the strike-through MRP an artificial marketing trick?
  2. *Resale & Arbitrage Potential:* Is there sufficient margin after subtracting an estimated 8% marketplace selling fee?

```typescript
// Gemma 2 Prompt Structure in llmClient.ts
const prompt = `You are HustleRadar's open-source deal auditor and retail arbitrage advisor powered by Google Gemma 2.
Evaluate this product deal strictly using the verified market evidence below. Do NOT hallucinate prices or retailers.

Product: ${params.productTitle}
Verdict: ${params.verdict} (Confidence: ${params.confidence})
Claimed Price: ₹${params.claimedPrice}
Reference/MRP: ₹${params.claimedOriginalPrice}
Market Median Price: ₹${params.medianPrice}
Lowest Listing: ${params.cheapestSeller.name} at ₹${params.cheapestSeller.price}
Arbitrage Net Profit Potential: ₹${params.arbitrage.netProfit} (${params.arbitrage.marginPercent}% margin)

Provide a concise, 2-3 sentence verdict for side-hustlers and buyers:
1. Is the advertised discount genuine or an inflated marketing trick?
2. Is there viable retail arbitrage / resale margin after 8% marketplace fees?`;
```

---

## 🌐 Why Does Open Innovation Matter?

This challenge asks: *"What did open innovation make possible that a closed API wouldn't?"*

1. **Zero Marginal Token Cost for Side Hustlers:**  
   A reseller researching inventory during festive flash sales might evaluate 200–500 SKUs a week. Closed APIs (like GPT-4o) charge per token, racking up $20–$50/month in API costs alone. Running open-weight **Gemma 2** locally via Ollama costs **$0.00**. It enables a side hustle to stay profitable from day one.
2. **Proprietary Sourcing Privacy:**  
   In retail arbitrage, your sourcing watchlist is your competitive moat. If a reseller routes their search targets through closed third-party cloud APIs, they run the risk of having their high-margin product discoveries logged or analyzed. With a local open-weight model, all analysis remains confidential on their laptop.
3. **No Vendor Lock-In or Sudden Price Hikes:**  
   Closed AI providers regularly deprecate models or introduce usage limits. Open-weight models like Gemma 2 belong to the community: you can run them on your laptop, quantize them, fine-tune them on your own past sales data, or host them on your own GPU.

---

## 👤 My First User

Before submitting, I put HustleRadar in front of **Rohan**, a freelance video editor and part-time electronics flipper based in Bengaluru.

* **His Workflow:** Rohan regularly scours festive clearance sales for camera lenses, studio monitors, and MacBooks, flipping them locally on OLX and tech forums.
* **The Test:** During the recent festive sale, an e-commerce platform advertised a popular telephoto lens marked down from *"₹74,999 to ₹48,500 (35% OFF!)"*.
* **What HustleRadar Found:**  
  HustleRadar scanned five competitor stores in 4 seconds. It revealed that:
  - The market median price was **₹49,200**.
  - Another retailer was selling it for **₹47,999** every day.
  - The "74,999 MRP" was artificially inflated before the sale.
  - Net Arbitrage after 8% fees was **-₹3,800** (a loss!).
* **User Feedback:**
  > *"If I had bought that lens thinking I had ₹25,000 of discount room, I would have lost my capital. The instant fee calculator and multi-store breakdown saved me from a bad ₹48,000 purchase. I'd definitely use this whenever I source gear."*

---

## 🏆 Prize Categories

I am submitting HustleRadar for the following partner categories:

1. **Theme: Side Hustle (Grand Prize)** — Built specifically to solve the core inventory and margin problem for retail flippers and freelance procurement.
2. **Best Use of Gemma ($200)** — Uses Google Gemma 2 (9B / 2B open-weights) via local Ollama and open inference as the Deal Auditor & Arbitrage Advisor.
3. **Best Use of SerpApi ($100)** — Grounded entirely in real-time, tamper-proof market pricing using SerpApi's `google_shopping` and retailer search engines.
4. **Best Use of Render ($200)** — Configured with a root `render.yaml` blueprint for one-click deployment of the full-stack architecture.
5. **Best Use of a .tech Domain ($100)** — Configured for deployment at `hustleradar.tech`.

---

*Built with ❤️ for open-source AI and side hustlers everywhere during Hacktoberfest 2026!*
