# HealthEquity AI

**Detecting health inequality hotspots with machine learning and actionable insights.**

Built at the 2026 Hackathon (March 27–29) by a cross-disciplinary team of Medicine, Data Science, and Policy.

---

## What it does

HealthEquity AI is an AI-powered decision-support platform that transforms routine health survey data into an interactive inequality map. It detects which counties and constituencies are falling behind, clusters them by deprivation profile, and generates targeted intervention recommendations using the Mistral AI API.

**Core workflow: Detect → Analyse → Act**

- **Detect** — K-means clustering on 23 DHS health indicators ranks all 47 Kenyan counties by a composite inequality score
- **Analyse** — Drill into any county to see a sub-constituency heatmap, indicator breakdowns, and cluster classification
- **Act** — AI-generated, clinically framed intervention plans per county/constituency via Mistral API

---

## Running the dashboard

The dashboard is a single HTML file that fetches local JSON data. You must serve it via a local HTTP server — double-clicking the file will not work (browser blocks local `fetch()` calls).

**Option 1 — Python (recommended):**
```bash
python -m http.server 8080
```
Then open `http://localhost:8080` in your browser.

**Option 2 — VS Code Live Server:**
Right-click `index.html` in the VS Code explorer → Open with Live Server.

---

## Project structure

```
2026hackaton/
├── index.html                  # Full dashboard (HTML + CSS + JS, single file)
├── kenya_data.json             # County scores, indicators, sub-county data
├── kenya_counties.geojson      # County border shapes (GADM level 1)
├── kenya_subcounties.geojson   # Constituency border shapes (GADM level 2)

```

---

## Regenerating the data

`kenya_data.json` is pre-built and committed. To regenerate it from the live DHS API:

```bash
pip install requests numpy scipy scikit-learn
```

**To run for a different DHS country**, change `countryIds` in `build_data.py`:
```python
params = {
    "countryIds": "NG",   # Nigeria, SN = Senegal, ET = Ethiopia, etc.
    ...
}
```

---

## Data sources

| Source | What | URL |
|---|---|---|
| DHS Program API | 23 health indicators, Kenya 2022, county level | `api.dhsprogram.com` |
| GADM | County and constituency GeoJSON boundaries | `gadm.org` |
| Mistral AI | AI intervention recommendations | `api.mistral.ai` |

Sub-county (constituency) health scores are **synthetic** — generated as random variation around the parent county score for demo purposes.

---

## Health indicators used

Fetched from the Kenya DHS 2022 survey across 47 counties:

| Category | Indicators |
|---|---|
| Child Health | Basic vaccination, DPT3, Measles, ORS treatment, Diarrhoea prevalence, Fever prevalence |
| Child Nutrition | Underweight, Stunting, Wasting |
| Reproductive Health | Facility delivery, ANC visits (4+), Cervical cancer screening |
| Family Planning | Modern contraceptive use, Unmet need |
| Infant & Child Mortality | Neonatal mortality rate |
| Water & Sanitation | Improved water source, Improved sanitation |
| Education | Female literacy rate, Women with secondary education |
| Fertility | Total fertility rate, Teenage childbearing |
| Malaria | Households with ITN |
| HIV/AIDS | HIV prevalence (adults 15-49) |

---

## AI configuration

The Mistral API key is hardcoded in `index.html` at the top of the `<script>` block:


## Clustering

Counties are grouped into 4 clusters by K-means on the full indicator matrix:

| Cluster | Meaning |
|---|---|
| Healthcare access gap | Low vaccination and facility delivery — deploy mobile health units |
| Infrastructure deprivation | Critical water/sanitation gaps — WASH interventions |
| Nutrition crisis zone | Elevated stunting and underweight — nutrition programmes |
| Education & empowerment gap | Low female literacy — education and CHW integration |
| Mixed deprivation | Multiple moderate signals — multi-sectoral approach |

---

## Dashboard features

- **Choropleth map** — all 47 counties coloured by inequality score (green → amber → red)
- **Risk filter** — sidebar filters map to critical / medium / low counties
- **Indicator toggles** — enable/disable individual indicators and watch scores recompute
- **County drill-down** — click any county to zoom in; surrounding counties dim and become non-interactive
- **Sub-county heatmap** — constituency-level score overlay with map tiles visible underneath
- **Constituency report** — click any constituency in the heatmap to load its data and AI recommendation
- **AI recommendation panel** — Mistral-generated intervention plan, falls back to template if API unavailable
- **Back to Kenya** — returns to full-country view

---

## Tech stack

| Layer | Technology |
|---|---|
| Map | Leaflet.js 1.9.4 + OpenStreetMap tiles |
| Data pipeline | Python, requests, numpy, scipy, scikit-learn |
| AI layer | Mistral API (`mistral-large-latest`) |
| Frontend | Vanilla JS, no framework |
| Fonts | IBM Plex Sans, IBM Plex Mono, Lora (Google Fonts) |

---

## Team

Medicine / Public Health · Data Science & ML · Policy & Management
