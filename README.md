# 🔭 CareerLens — US Job Market Visualizer

> An interactive research tool for visually exploring the full US job landscape — powered by Bureau of Labor Statistics data and LLM-scored AI exposure metrics.

**🌐 Live Demo: [prajitdatta.github.io/career-lens](https://prajitdatta.github.io/career-lens/)**  
**👤 Author: [Prajit Datta](https://github.com/prajitdatta) · [prajitdatta.github.io](https://prajitdatta.github.io/)**

---

## What is CareerLens?

CareerLens scrapes, parses, and visualizes all **342 occupations** from the [Bureau of Labor Statistics Occupational Outlook Handbook](https://www.bls.gov/ooh/), covering **143 million jobs** across every sector of the US economy.

Each occupation is rendered as a rectangle in an interactive treemap where:
- **Area** is proportional to total employment
- **Color** reflects the selected metric — toggle between:
  - 📈 BLS projected growth outlook
  - 💰 Median annual pay
  - 🎓 Education requirements
  - 🤖 Digital AI exposure score

This is **not** a formal economic report — it's a visual development tool for exploring BLS data and testing LLM-scoring pipelines.

---

## LLM-Powered Coloring

The repo includes scrapers, parsers, and a full pipeline for scoring occupations with any custom LLM prompt. The **"Digital AI Exposure"** layer is one example — it estimates how much current AI (primarily digital) will reshape each occupation.

**You can fork this and write your own prompt for any question:**
- Exposure to humanoid robotics
- Offshoring/outsourcing risk
- Climate transition impact
- Regulatory vulnerability

See [`score.py`](score.py) for the scoring pipeline.

### ⚠️ What "AI Exposure" is NOT

- It does **not** predict that a job will disappear. Software developers score 9/10 because AI is transforming their work — but demand for developers could easily *grow* as each becomes more productive.
- It does **not** account for demand elasticity, latent demand, regulatory barriers, or human preference.
- Scores are rough LLM estimates (Gemini Flash via OpenRouter), not rigorous predictions.

---

## Data Pipeline

```
scrape.py → parse_detail.py → process.py → make_csv.py → score.py → build_site_data.py → site/
```

| Step | Script | Output | Description |
|------|--------|--------|-------------|
| 1 | `scrape.py` | `html/` | Playwright (non-headless) downloads 342 BLS occupation pages |
| 2 | `parse_detail.py` + `process.py` | `pages/` | BeautifulSoup converts HTML → clean Markdown |
| 3 | `make_csv.py` | `occupations.csv` | Extracts structured fields: pay, education, job count, growth, SOC code |
| 4 | `score.py` | `scores.json` | Sends each occupation to LLM with scoring rubric → AI Exposure 0–10 |
| 5 | `build_site_data.py` | `site/data.json` | Merges CSV + scores into compact frontend payload |
| 6 | `site/index.html` | — | Interactive treemap visualization |

---

## Key Files

| File | Description |
|------|-------------|
| [`occupations.json`](occupations.json) | Master list of 342 occupations with title, URL, category, slug |
| [`occupations.csv`](occupations.csv) | Summary stats: pay, education, job count, growth projections |
| [`scores.json`](scores.json) | AI exposure scores (0–10) with rationales for all 342 occupations |
| [`prompt.md`](prompt.md) | All data in a single ~45K-token file for pasting into any LLM |
| [`html/`](html/) | Raw HTML pages scraped from BLS (~40 MB) |
| [`site/`](site/) | Static website (the treemap visualization) |

---

## Setup

Requires [uv](https://github.com/astral-sh/uv):

```bash
uv sync
uv run playwright install chromium
```

Add your OpenRouter API key to a `.env` file:

```
OPENROUTER_API_KEY=your_key_here
```

---

## Usage

```bash
# 1. Scrape BLS pages (cached in html/ — only run once)
uv run python scrape.py

# 2. Parse HTML → Markdown
uv run python process.py

# 3. Extract structured CSV
uv run python make_csv.py

# 4. Score AI exposure via LLM (uses OpenRouter API)
uv run python score.py

# 5. Build frontend data
uv run python build_site_data.py

# 6. Serve locally
cd site && python -m http.server 8000
```

Open [http://localhost:8000](http://localhost:8000) in your browser.

---

## LLM Prompt File

[`prompt.md`](prompt.md) packages all data — aggregate statistics, tier breakdowns, exposure by pay/education, BLS growth projections, and all 342 occupations with scores and rationales — into a single file (~45K tokens). Paste it into any LLM for a data-grounded conversation about AI's impact on the job market without running any code.

Regenerate it with:

```bash
uv run python make_prompt.py
```

---

## Author

**Prajit Datta**  
🔗 GitHub: [github.com/prajitdatta](https://github.com/prajitdatta)  
🌐 Website: [prajitdatta.github.io](https://prajitdatta.github.io/)

---

## License

MIT — fork it, score it differently, ship your own version.
