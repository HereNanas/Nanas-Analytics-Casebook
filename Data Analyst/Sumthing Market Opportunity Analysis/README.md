# 🏗️ SumThing Market Opportunity Analysis: B2B Go-to-Market Strategy for AI Safety Monitoring

A business analytics case study identifying which regions in Java, Indonesia offer the strongest B2B sales opportunity for an AI-powered construction safety monitoring product; combining workplace accident data with construction company density data to prioritize sales, pilot, and marketing efforts.

> **Note:** This project was submitted as a team assignment (Kelompok 1). See [My Role](#-my-role-in-this-project) below for the specific scope of my individual contribution.

## 🎯 Business Problem

**SumThing** is an AI safety monitoring product for the construction industry (PPE detection, real-time incident monitoring, worker surveillance via computer vision). With **1,840 recorded construction accidents** across 4 Java provinces over the past 3 years (source: BPJS Ketenagakerjaan), safety oversight in the sector remains largely manual, with limited real-time monitoring or automated PPE detection.

**Main question:** How should SumThing prioritize its marketing and sales regions based on construction accident rates and construction company density across the 4 Java provinces (Jawa Timur, Jawa Tengah, Jawa Barat, Banten)?

This breaks down into four sub-questions:
1. Where is the highest concentration of construction companies?
2. What does the market opportunity look like based on risk and construction activity?
3. What should SumThing's go-to-market strategy and target segments be?
4. Which regional hotspots are best suited for an initial AI Safety pilot implementation?

## 🗂️ Data Sources

| Dataset | Period | Source | Description |
|---|---|---|---|
| Construction Workplace Accidents | 2023–2025 | BPJS Ketenagakerjaan | Construction-sector accident counts per province, filtered to 4 Java provinces |
| Construction Company Directory | 2025 | BPS (Statistics Indonesia), per province | Number and legal form (PT, CV, Cooperative, etc.) of construction companies per Kabupaten/Kota in Jatim, Jateng, Jabar, and Banten |

## ⚙️ Analytical Approach

1. **Data cleaning & integration:** merged accident data and company registry data across 4 provinces and 104 kabupaten/kota, standardizing province naming and handling multi-header Excel exports
2. **Construction Company Density Analysis:** ranked provinces and cities by number of active construction companies
3. **Opportunity Score & Risk Density Analysis:**
   - `Opportunity Score = Construction Accident Frequency (2025) + Construction Company Density`
   - `Risk Density = accidents per 1,000 companies` — a normalized measure of relative danger, independent of market size
4. **B2B Market Prioritization (Weighted Scoring Model):**
   - `Priority Score = Opportunity Score (40%) + Market Share (30%) + Risk Density (30%)`, normalized 0–100 via `MinMaxScaler.`
   - Thresholds: **≥70 = Sales Priority**, **40–69 = Pilot Project**, **<40 = Canvassing**
5. **Regional Hotspot Analysis:** identified kabupaten/kota combining high company density and high risk density as candidates for AI Safety pilot implementation
6. **Market Segmentation:** grouped provinces and kabupaten/kota by accident level and company density (above/below median), and separately by company characteristics (business scale, legal form)

## 📈 Key Findings

### Market Concentration
- **65,587 construction companies** across the 4 provinces combined
- **Jawa Timur dominates** with 24,564 companies (**37.4%** market share), followed by Jawa Barat with 17,919 (27.3%)
- **Surabaya is the single largest hotspot nationally**, with 4,146 construction companies

### Opportunity & Risk
| Province | Opportunity Score | Risk Density (per 1,000 companies) | B2B Priority Score | Category |
|---|---|---|---|---|
| Jawa Timur | 25,203 | 26.0 | **76.9** | **Sales Priority** |
| Jawa Barat | 18,629 | 39.6 | 66.8 | Pilot Project |
| Jawa Tengah | 23,762 | — | 31.5 | Canvassing |
| Banten | — | **44.4** (highest) | 30.0 | Canvassing |

- **Jawa Timur leads on scale**, not on danger — its high opportunity score comes from sheer company volume, not the highest accident rate.
- **Banten has the highest risk density** (44.4 accidents per 1,000 companies), signaling urgency for safety solutions even though its overall market is smaller.

### Regional Hotspots for AI Safety Pilot
| Rank | Location | Rationale |
|---|---|---|
| ★★★★★ | Tangerang Selatan | High risk density + proximity to Jakarta → top pilot candidate |
| ★★★★★ | Surabaya | Highest company density → primary sales target |
| ★★★★ | Bandung / Bekasi | Combined high risk + high density → second pilot candidate |
| ★★★ | Tangerang Kota | High risk density, growing market |
| ★★★ | Semarang | Central Java's construction hub |

## 🎯 Strategic Recommendations

| Region | B2B Priority | Strategy | Key Actions |
|---|---|---|---|
| **Jawa Timur** | Sales Priority (76.9) | Direct Sales & Enterprise | Dedicated sales team + free workshops in Surabaya; bundle products for large/state-owned contractors; target 5 enterprise contracts in Q1 |
| **Jawa Barat** | Pilot Project (66.8) | Trial & Partnership | Free pilot in Bandung & Bekasi (10 companies); partner with local contractor associations |
| **Jateng & Banten** | Canvassing (~31) | Digital Marketing & Awareness | "Reduce Construction Accidents" Google Ads campaign; online K3 (workplace safety) webinars; target CPL < Rp 50,000 |

**Special note:** despite Banten falling into the "Canvassing" category overall, **Tangerang Raya (Tangerang + Tangerang Selatan)** is flagged as a micro-priority for the AI Safety pilot given its exceptionally high risk density and proximity to Jakarta.

### 6-Month Action Plan

- **Short term (1–2 months):** focus sales in Jawa Timur; begin pilot preparation in Tangerang Selatan; launch digital awareness campaign
- **Medium term (3–4 months):** build contractor partnerships in Surabaya; run official pilot in Tangsel/Bandung (10 companies); publish case studies; expand sales into Jawa Barat
- **Long term (5–6+ months):** establish SumThing as Indonesia's construction AI safety monitoring partner; develop predictive safety monitoring; scale to Bandung, Bekasi, Semarang; target enterprise clients across all of Java

**Key OKRs:** 5 active enterprise contracts · 30% pilot-to-paid conversion rate · recognized as the #1 AI Safety partner in East Java's construction sector

## 🛠️ Tech Stack

Python · Pandas · NumPy · Matplotlib · Seaborn · Excel (data source & output) · PowerPoint (stakeholder presentation)

## ⚠️ Known Limitations & Future Improvements

- **Opportunity Score is an unweighted sum** (accidents + company count) in the earlier stage of the analysis, before the more rigorous weighted/normalized scoring model was introduced for B2B prioritization — worth applying the same normalized approach consistently throughout.
- Several plotting cells use a deprecated Seaborn `palette` argument without `hue`, which triggers `FutureWarning`s in recent Seaborn versions (cosmetic only, safe to update by assigning `hue` explicitly).
- Analysis currently covers only the 4 Java provinces; expanding to other regions would require re-validating whether the same opportunity/risk scoring thresholds still make sense at a national scale.

## 📁 Repository Structure

```
sumthing-market-opportunity-analysis/
├── README.md
├── notebook/
│   └── Sumthing_Konstruksi_jawa.ipynb
├── presentation/
│   └── SumThing_Market_Opportunity.pptx
├── data/
│   └── Kecelakaan kerja 3 tahun
├── results/
│   ├── company_density_province.png
│   ├── opportunity_score_bar.png
│   ├── b2b_priority_score.png
│   └── regional_hotspot_map.png
└── requirements.txt
```

## ▶️ How to Run

```bash
git clone https://github.com/HereNanas/Nanas-Analytics-Casebook.git
cd Nanas-Analytics-Casebook/data-analyst/sumthing-market-opportunity-analysis
pip install -r requirements.txt
jupyter notebook notebook/Sumthing_Konstruksi_jawa.ipynb
```

## 📊 Full Presentation

See [`presentation/SumThing_Market_Opportunity.pptx`](presentation/SumThing_Market_Opportunity.pptx) for the complete stakeholder-facing deck, including the business background, full strategic framework, and 6-month action plan with OKRs.

## 🙋 My Role in This Project

While this was submitted as a team assignment, I independently carried out the full pipeline for this project end-to-end:

- **Data collection & integration** — sourcing and combining BPJS Ketenagakerjaan accident data with BPS construction company registries across 4 provinces and 104 kabupaten/kota
- **Data analysis** — company density analysis, opportunity/risk scoring, weighted B2B prioritization model, regional hotspot identification, and market segmentation
- **Coding** — all data cleaning, analysis, and visualization implemented in Python (Pandas, Matplotlib, Seaborn)
- **Business strategy** — translating the analysis into go-to-market recommendations, regional prioritization, and the 6-month action plan with OKRs
- **Reporting & presentation** — writing the full stakeholder-facing deck and narrative

## 👤 Author

**Nastiti Susetyo Fanany Putri**
[Google Scholar](https://scholar.google.com/citations?view_op=list_works&hl=id&hl=id&user=sLmEtkcAAAAJ) · [GitHub](https://github.com/HereNanas)
