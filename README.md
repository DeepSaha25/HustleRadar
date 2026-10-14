# HustleRadar ⚡

> **The Open-Source AI Market Arbitrage & Deal Discovery Radar for Side Hustlers, Resellers & Smart Procurement.**  
> *Audits retailer discounts, exposes fake clearance sales, and calculates net resale margins in real time.*  
> **Built for Hacktoberfest Open-Source AI Challenge: Week 2 (Theme: Side Hustle) 🎃**

[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat-square&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![React](https://img.shields.io/badge/React_18-20232A?style=flat-square&logo=react&logoColor=61DAFB)](https://reactjs.org/)
[![Vite](https://img.shields.io/badge/Vite-646CFF?style=flat-square&logo=vite&logoColor=white)](https://vitejs.dev/)
[![Express](https://img.shields.io/badge/Express-000000?style=flat-square&logo=express&logoColor=white)](https://expressjs.com/)
[![Google Gemma 2](https://img.shields.io/badge/Open--Weight_AI-Google_Gemma_2-8E75C4?style=flat-square)](https://ai.google.dev/gemma)
[![SerpApi](https://img.shields.io/badge/Grounding-SerpApi-blue?style=flat-square)](https://serpapi.com/)
[![Render](https://img.shields.io/badge/Deploy-Render-46E3B7?style=flat-square&logo=render)](https://render.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)

---

## 💡 The Core Problem: Why Side Hustlers Lose Money on "Deals"

Side hustlers, electronics flippers, dropshippers, and freelance studios purchase high-ticket inventory (MacBooks, cameras, audio gear, smartphones) to resell or run their business.

However, retail platforms deploy aggressive dark patterns:
1. **Artificial MRP Anchoring:** E-commerce stores regularly inflate reference prices before flash sales (e.g., marking a ₹30,000 lens up to ₹50,000 to advertise a "60% discount").
2. **The "Sale Trap":** A flipper buys clearance stock thinking they have a 40% margin, only to discover competitor stores sell that exact SKU for even less every single day.
3. **Closing Fee Realities:** Beginners forget that marketplace cuts (8–15%) and return risks eliminate thin margins.

**HustleRadar solves this by pairing Google Gemma 2 (open-weights) with SerpApi's live multi-store market scanner.**

---

## ⚡ Key Features

* **⚡ Side Hustle Arbitrage Radar:** Computes gross & net margins based on the true market floor vs market median, deducting realistic 8% marketplace seller fees.
* **🤖 Google Gemma 2 AI Audit:** Evaluates deal authenticity and provides structured flip recommendations without hallucination. Runs locally via Ollama (`gemma2:9b` / `gemma2:2b`) or open cloud inference.
* **🌐 Live Multi-Store Market Grounding:** Scans Amazon, Flipkart, Croma, Reliance Digital, and independent retailers simultaneously via SerpApi.
* **🛡️ Strict Variant & Condition Filtering:** Excludes refurbished stock, accessories (cases/cables), and mismatched RAM/storage configurations so price comparisons are 100% like-for-like.
* **📊 Scenario & Discount Stacking Calculator:** Lets buyers model bank card offers, instant coupons, and delivery costs with real-time recalculation.
* **🗂️ Auditable Evidence Receipts:** Clickable store links, seller identities, extraction timestamps, and dated web mentions.

---

## 🏆 Hacktoberfest 2026 Partner Categories Alignment

HustleRadar is engineered to compete across multiple partner tracks in **Week 2: Side Hustle**:

| Category | How HustleRadar Qualifies |
| :--- | :--- |
| **Theme: Side Hustle (Grand Prize)** | Directly solves the core workflow problem for retail arbitrage flippers and freelance procurement: preventing bad inventory buys and calculating instant flip ROI. |
| **Best Use of Gemma ($200)** | Integrates Google's **Gemma 2** (9B / 2B open-weights) via local Ollama or open inference API as the Deal Auditor & Arbitrage Advisor. |
| **Best Use of SerpApi ($100)** | Uses SerpApi's `google_shopping` and retailer engines to provide real-time, tamper-proof web grounding for the Gemma model. |
| **Best Use of Render ($200)** | Pre-configured with a root `render.yaml` blueprint for one-click full-stack deployment on Render. |
| **Best Use of a .tech Domain ($100)** | Designed for deployment at `hustleradar.tech` using the free Hacktoberfest participant promo. |

---

## 🧠 Why Open Innovation Matters for Side Hustlers

The challenge asks: *"Tell us where your open-based approach worked better than a closed one."*

1. **Zero Marginal Token Cost:**  
   A serious flipper audits 200–500 SKUs a week during sale seasons. Using closed proprietary APIs (like GPT-4o) adds recurring SaaS bills that destroy micro-business profitability. Running open-weight **Gemma 2** locally via Ollama costs **$0.00 per token**.
2. **Proprietary Sourcing Privacy:**  
   Arbitrageurs fiercely protect their product niches and suppliers. Running inference on a local open-weight model keeps their watchlist and margin data private on their machine.
3. **No Vendor Lock-In:**  
   If a closed API changes pricing, deprecates endpoints, or flags web-scraping queries, the business breaks. Open-weight Gemma 2 can be fine-tuned, quantized, or served anywhere (laptop, private GPU, or Render).

---

## 🏗️ Architecture

```text
Browser (React 18 + TypeScript + Vite + Tailwind/Glassmorphism)
  Landing → Deal & Arbitrage Radar → Live Streamed Report
                         ├─ Arbitrage Margin & Flip Feasibility Card
                         ├─ Multi-Retailer Price Comparison Matrix
                         ├─ Scenario & Bank Discount Calculator
                         └─ Gemma 2 Deal Rationale & Proof Receipts
                                       │
                                       ▼ HTTP / NDJSON Stream
Express Server (Node.js + TypeScript)
  /api/check-deal
  /api/check-deal/stream
                         ├─ SerpApi Orchestrator (Google Shopping + Retailer Search)
                         ├─ Strict Variant & INR Filtering
                         ├─ Deterministic Arbitrage Engine (Median & Fees)
                         └─ Google Gemma 2 (Local Ollama / Open API Inference)
```

---

## 🚀 Quick Start (Local Setup)

### 1. Clone the repository
```bash
git clone https://github.com/DeepSaha25/HustleRadar.git hustleradar
cd hustleradar
```

### 2. Install dependencies
```bash
npm install
```

### 3. Run Google Gemma 2 locally (via Ollama)
```bash
# Pull and start Gemma 2 (recommended 9B or lightweight 2B)
ollama run gemma2:9b
```

### 4. Configure Environment
Copy `.env.example` to `.env`:
```bash
cp .env.example .env
```
Fill in your credentials:
```env
SERPAPI_KEY=your_serpapi_key_here
OLLAMA_BASE_URL=http://127.0.0.1:11434
GEMMA_MODEL_NAME=gemma2:9b
```
*(Note: If no Gemma or SerpApi key is supplied, HustleRadar runs in **Demo Mode** with realistic local fixtures so you can test immediately without credentials!)*

### 5. Start the Application
```bash
npm run dev
```
* **Frontend:** [http://localhost:5173](http://localhost:5173)
* **Backend:** [http://localhost:8080](http://localhost:8080)

---

## 🧪 Testing

Run the automated test suite across all listing guards, variant deduplication, and streaming endpoints:
```bash
npm test
```
All 23 unit tests run with zero mock leakage.

---

## 🌐 Deploy to Render

HustleRadar includes a ready-to-use `render.yaml` Blueprint:
1. Connect this repository to your [Render](https://render.com) dashboard.
2. Select **New > Blueprint**.
3. Render automatically provisions the `hustleradar-api` (Node) and `hustleradar-web` (Static Site) services.

---

## 📄 License

MIT License. Designed and built with ❤️ for **Hacktoberfest Open-Source AI Challenge 2026**.
