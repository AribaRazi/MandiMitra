# 🌾 MandiMitra — A Gemma 4 Crop Market Advisor for Indian Farmers

*"Mitra" means friend. This is the friend who does the math before giving advice.*

MandiMitra helps Indian farmers decide whether to sell their harvest now or wait — combining real mandi price data, live weather forecasts, and a computed sell-now-vs-wait simulation into a plain-language, multilingual recommendation. All numbers are calculated by code; **Gemma 4 only explains them, it never invents them.**

Built for **Gemma for Bharat — GDGoC JIS University 2026**, Track: Bharat Data-to-Insight Pipeline.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Demo](#demo)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Setup Instructions](#setup-instructions)
- [Usage](#usage)
- [Limitations](#limitations)
- [Roadmap](#roadmap)
- [License](#license)

---

## Overview

A farmer holding harvested crop has a real, high-stakes question: sell today at the known price, or hold and hope prices rise — while weather and storage costs eat into the value of waiting. MandiMitra answers that question directly, with numbers the farmer can actually verify, in a language they actually speak.

## Features

- 📊 **Real market data** — cleaned Agmarknet mandi price records (commodity, state, district, market, price trends)
- 🌦️ **Live weather integration** — 7-day rainfall and temperature forecast via Open-Meteo, factored into the recommendation
- ⚖️ **Sell-Now vs. Wait simulator** — computes an actual rupee comparison between selling today and holding for N more days, accounting for a storage-cost estimate
- 🌐 **Multilingual output** — recommendations generated natively (not translated) in English, Hindi, Bengali, Tamil, and Telugu
- 🔍 **Full transparency** — every number shown in the advisory is visible and checkable in a raw-data view
- 🟢 **Confidence badges** — flags when a recommendation is based on limited data

## Demo

- **Live demo (temporary, session-dependent):** `<insert your Gradio share link here, and note it may be inactive if the Kaggle session isn't running>`
- **Demo video (YouTube):** https://youtu.be/DMVqajslhgE?feature=shared
- **Kaggle Notebook (recommended way to run this project):** 

## Architecture

```
Agmarknet CSV
   │  (clean: parse dates, drop nulls/outliers, normalize text)
   ▼
get_market_summary()  ──►  price stats (current, avg, trend %)
   │
   ▼
get_weather_forecast()  (Open-Meteo API)  ──►  7-day rain & temp forecast
   │
   ▼
simulate_sell_vs_wait()  ──►  computed ₹ comparison (sell now vs. hold N days)
   │
   ▼
──────────────────────────────────────────
        Gemma 4 (E2B-it)
  the ONLY text-generation step —
  synthesizes price + weather + simulation
  into one plain-language recommendation,
  natively in the chosen language
──────────────────────────────────────────
   │
   ▼
Gradio UI (tabs: Advisory · Price Trend · Sell vs Wait · Raw Data)
```

**Where Gemma sits:** Gemma receives only pre-computed, structured facts — it never calculates a price, a trend, or a rupee figure itself. Its role is narrowly scoped to fusing three independent data sources (market data, weather, the simulation result) into one coherent, localized recommendation. No external LLM API (OpenAI, Gemini, Claude, etc.) is used anywhere in this project — Gemma 4 is loaded and run locally via `transformers`.

## Tech Stack

| Component | Tool |
|---|---|
| LLM | Gemma 4, E2B-it (instruction-tuned), via Hugging Face `transformers` |
| Data processing | pandas |
| Weather | Open-Meteo API (free, no key required) |
| UI | Gradio (`gr.Blocks`) |
| Charts | Plotly |
| Runtime | Kaggle Notebook (free T4 GPU) |

## Setup Instructions

This project is built and intended to run as a **Kaggle Notebook**. Follow these steps for a clean run:

### 1. Open the notebook
Open `MandiMitra.ipynb` (or the linked public Kaggle notebook) in Kaggle's notebook editor.

### 2. Enable GPU and Internet
In the notebook's **Settings** panel (right sidebar):
- **Accelerator:** GPU T4 x2
- **Internet:** On *(required for the transformers upgrade, the Gemma 4 model download, and the live weather API)*

### 3. Add the dataset
Click **Add Input** → search for **"Agmarknet India Commodity Prices"** → add it. Confirm the path under `/kaggle/input/` matches the `DATA_PATH` variable in Step 2 of the notebook (update it if the exact filename differs).

### 4. Add the Gemma 4 model
Click **Add Input → Models** → search **"Gemma 4"** → select the **Transformers** framework → variation **`gemma-4-E2B-it`**. This attaches the model files under `/kaggle/input/models/...`.

### 5. Set up a Hugging Face token (if loading via Hugging Face Hub)
- Create a free token at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)
- Accept the Gemma license at `huggingface.co/google/gemma-4-E2B-it`
- In Kaggle: **Add-ons → Secrets** → add a secret named `HF_TOKEN` with your token value
- *(If using the Kaggle Models path from Step 4 instead, this step can be skipped — Kaggle handles authentication automatically.)*

### 6. Run the notebook
**Run → Restart & Run All.** The notebook installs a compatible `transformers` version, loads Gemma 4, cleans the dataset, and launches the Gradio interface — no manual intervention needed beyond the steps above.

### 7. Access the app
The final cell (`demo.launch(share=True)`) prints a public URL valid for that session. For a permanent link, deploy to Hugging Face Spaces from the notebook's terminal:
```bash
gradio deploy
```

## Usage

1. Select a **commodity** and **state**
2. Choose your **response language**
3. Toggle **weather forecast** on/off
4. Enter your **quantity held** (quintals) and **days you could wait**
5. Click **Get Advisory**
6. Review the **Advisory**, **Price Trend**, **Sell vs Wait**, and **Raw Data** tabs

## Limitations

- The sell-vs-wait projection assumes the recent 30-day price trend continues linearly — a simplification, not a forecast
- Storage-cost estimates are illustrative, not sourced from real logistics data
- Weather is resolved at state level, not district level
- Built on a static dataset snapshot, not a live-updating feed
- Not yet validated with real farmer users — treat outputs as decision support, not financial advice

See the full write-up for details.

## Roadmap

- Seasonal/non-linear price projection model
- District-level weather resolution
- Sourced, real storage-cost data
- Live-updating Agmarknet data feed
- User testing with real farmers

## License

This project was built for the **Gemma for Bharat — GDGoC JIS University 2026** hackathon. Code is provided as-is for evaluation purposes.

---

*Built with Gemma 4. No external LLM APIs used.*
