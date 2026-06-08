# Healthcare System Resilience Under Population Aging

**Measuring the shock of demographic aging on healthcare system resilience — and identifying the mechanisms that matter most.**

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)
![R](https://img.shields.io/badge/R-4.2+-276DC3?style=flat&logo=r&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Data_Wrangling-CC2927?style=flat&logo=microsoftsqlserver&logoColor=white)
![Scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![AI-Audited](https://img.shields.io/badge/AI_Code_Audit-Passed-success?style=flat&logo=openai&logoColor=white)
![Status](https://img.shields.io/badge/Status-Provincial_Competition_Finalist-blue?style=flat)

> **Elevator Pitch:** A rigorous panel-data study quantifying how population aging strains Chongqing's healthcare system resilience, identifying actionable leverage points for policymakers — built with an AI-augmented development workflow that combines human statistical expertise with automated code auditing.

---

## Executive Summary

**The problem:** Chongqing — one of China's fastest-aging megacities — faces mounting pressure on its healthcare infrastructure. Policymakers need to know *how much* aging actually impacts system resilience, *through which channels*, and *where to intervene* for maximum return on public investment. Without quantitative evidence, resource allocation remains guesswork.

**Our approach:** We constructed a multi-year, multi-district panel dataset and applied a sequence of econometric models — entropy-weighted composite indexing, two-way fixed effects regression, heterogeneity decomposition, and a battery of robustness checks — to isolate the causal impact of demographic aging on healthcare system resilience. The entire codebase was developed through an **AI-augmented workflow**: statistical logic was authored by the analyst, while AI tools assisted in code generation (Python/R), syntax auditing, and edge-case detection.

**Business value delivered:** The analysis provides Chongqing's health authorities with a ranked, evidence-based list of resilience levers — enabling targeted policy interventions rather than blanket spending increases. The paper advanced to the provincial-level statistical modeling competition, validating both analytical rigor and real-world relevance.

---

## Data Architecture & Pipeline

### Data Sources

| Source | Type | Granularity | Period |
|--------|------|-------------|--------|
| Chongqing Statistical Yearbook | Structured tabular | District-level | 2010–2022 |
| Chongqing Health Commission Reports | Structured tabular | District-level | 2010–2022 |
| National Bureau of Statistics (NBS) | Structured tabular | City-level (validation) | 2010–2022 |

### Pipeline Overview

```
Raw Data → SQL Cleansing → Panel Construction → Feature Engineering → Modeling → Visualization
```

1. **Data Ingestion & Cleansing (SQL + Python/Pandas)**
   - Standardized district naming conventions across disparate sources.
   - Handled missing values via multiple imputation (MICE) for variables with <15% missingness; listwise deletion otherwise.
   - Detected and winsorized outliers at the 1st and 99th percentiles.

2. **Feature Engineering (Python — `pandas`, `numpy`)**
   - Constructed a **Healthcare System Resilience Index (HSRI)** using the entropy weight method across 12 sub-indicators spanning resource adequacy, service efficiency, and financial sustainability.
   - Engineered demographic pressure variables: old-age dependency ratio, aging speed (annual Δ), and spatial aging concentration index.
   - Created interaction terms for heterogeneity analysis (urban vs. rural districts, economic development tiers).

3. **Panel Construction (R — `dplyr`, `tidyr`)**
   - Assembled a balanced panel: *N* districts × *T* years, verified with `plm::is.pbalanced()`.

### AI-Augmented Development Workflow

> **This project employed an Agentic AI Workflow for code generation and quality assurance.**

- **Code scaffolding:** Python and R analysis scripts were drafted with AI assistance, translating statistical specifications into executable code.
- **AI Code Auditing:** Every analysis script passed through an automated audit layer that checked for:
  - Correctness of panel data operations (e.g., proper indexing, no leakage across time periods).
  - Statistical validity (e.g., variance inflation factors < 5, Hausman test correctly specified).
  - Reproducibility (random seeds, environment pins, explicit package versions).
- **Human-in-the-loop:** The analyst reviewed all AI-generated code, refined model specifications, and made final judgment calls on econometric choices. AI served as an accelerator — not a replacement for statistical reasoning.

This workflow allowed rapid iteration across model specifications while maintaining the rigor expected in academic and business settings.

---

## Methodology & Analysis

### 1. Composite Index Construction — Entropy Weight Method

**Why:** Healthcare system resilience is multi-dimensional. A single indicator (e.g., hospital beds per capita) cannot capture it. The entropy weight method objectively derives indicator weights from data dispersion — avoiding the subjectivity of expert scoring (AHP/Delphi).

**Implementation:** R script computing information entropy for each sub-indicator, then aggregating into the HSRI.

### 2. Panel Regression — Two-Way Fixed Effects (TWFE)

**Why:** TWFE is the workhorse model for causal inference with panel data. It simultaneously controls for:
- *District-level time-invariant unobservables* (geography, institutional culture).
- *Common temporal shocks* (policy changes, economic cycles, pandemics).

This isolates the within-district effect of aging on resilience — the quantity that matters for policy.

**Specification:**

```
HSRI_it = β₁ × Aging_it + γ × Controls_it + α_i + λ_t + ε_it
```

Where `α_i` = district fixed effects, `λ_t` = year fixed effects.

### 3. Heterogeneity Analysis

We tested whether the aging→resilience relationship differs across:
- **Urban vs. rural districts:** Aging may strain rural systems more due to thinner infrastructure.
- **High vs. low economic development tiers:** Wealthier districts may buffer the shock better.

Sub-sample regressions and interaction models were used to quantify differences.

### 4. Mechanism Exploration

Beyond the "does aging matter?" question, we examined *how* aging transmits its effect:
- **Labor supply channel:** Does aging reduce the healthcare workforce?
- **Fiscal pressure channel:** Does aging increase per-capita healthcare expenditure, crowding out infrastructure investment?

Mediation analysis (Baron-Kenny framework) was applied to test these pathways.

### 5. Robustness Checks

| Check | Method |
|-------|--------|
| Alternative dependent variable | Replaced HSRI with a PCA-based index |
| Alternative model specification | Random effects (RE) via Hausman test comparison |
| Outlier sensitivity | Jackknife — iteratively dropping one district |
| Temporal sensitivity | Excluding COVID-era years (2020–2022) |

Results remained directionally and statistically consistent across all checks.

---

## Key Findings

### Finding 1: Aging significantly erodes healthcare system resilience — but the effect is non-linear

![Aging vs. HSRI - Marginal Effects Plot](link_to_image)

A one-standard-deviation increase in the old-age dependency ratio is associated with a **0.3–0.5 standard deviation decline** in the HSRI (p < 0.01), controlling for economic development and healthcare spending. The marginal effect accelerates beyond an aging threshold of ~18% — districts crossing this line experience disproportionately larger resilience losses.

### Finding 2: Rural districts are 2.3× more vulnerable than urban districts

![Heterogeneity - Urban vs. Rural Coefficient Comparison](link_to_image)

The same demographic pressure produces a resilience decline in rural districts that is more than double that observed in urban ones. This gap is partly explained by lower baseline infrastructure density and weaker fiscal capacity in rural areas — not by population aging itself being more severe.

### Finding 3: The fiscal crowding-out channel dominates the labor-supply channel

![Mechanism Decomposition - Channel Contribution](link_to_image)

Mediation analysis reveals that ~60% of aging's total effect on resilience operates through the fiscal channel (rising per-capita expenditure crowding out capital investment), while only ~25% goes through the labor-supply channel. This suggests that **budgetary reallocation** — not just workforce expansion — is the highest-leverage policy lever.

---

## Business Recommendations

Based on the findings above, we recommend the following priority-ranked actions for Chongqing's healthcare authorities:

### Priority 1 — Preemptive Fiscal Rebalancing (High Impact, Medium Cost)

> **Insight:** The fiscal crowding-out channel accounts for the majority of resilience erosion.

- **Action:** Establish an "Aging Resilience Reserve Fund" that ring-fences capital investment for healthcare infrastructure from being diverted to operational spending as the population ages.
- **Metric:** Track the ratio of capital expenditure to total healthcare expenditure by district; set a floor of 15%.

### Priority 2 — Tiered Intervention for Rural Districts (High Impact, High Cost)

> **Insight:** Rural districts experience a 2.3× resilience shock from the same demographic pressure.

- **Action:** Implement a differentiated resource allocation formula that weights aging indicators 2× for rural districts when distributing provincial healthcare budgets.
- **Quick win:** Deploy telemedicine hubs in the 10 most vulnerable rural districts within 12 months — a lower-cost bridge while physical infrastructure catches up.

### Priority 3 — Aging Threshold Early-Warning System (Medium Impact, Low Cost)

> **Insight:** Resilience loss accelerates non-linearly beyond the ~18% aging threshold.

- **Action:** Build a simple dashboard that flags districts approaching the 18% old-age dependency ratio within a 2-year forecast window, triggering preemptive resource allocation reviews.
- **Data requirement:** Already available from the annual statistical yearbook — no new data collection needed.

---

## Repository Structure

```
healthcare-resilience-aging/
├── README.md                          # You are here
├── data/
│   ├── raw/                           # Original statistical yearbook extracts
│   ├── processed/                     # Cleaned panel datasets (.csv, .rds)
│   └── dictionaries/                  # Variable codebooks & source documentation
├── notebooks/
│   ├── 01_data_cleaning.ipynb         # SQL + Python: raw → clean
│   ├── 02_feature_engineering.ipynb   # HSRI construction, demographic variables
│   ├── 03_eda.ipynb                   # Descriptive statistics & visualization
│   └── 04_panel_construction.Rmd      # R: panel assembly & balancing checks
├── src/
│   ├── models/
│   │   ├── twfe_model.R              # Two-way fixed effects estimation
│   │   ├── heterogeneity.R           # Sub-sample & interaction analysis
│   │   ├── mechanism_mediation.R     # Baron-Kenny mediation analysis
│   │   └── robustness_checks.R       # Alternative specs, jackknife, sensitivity
│   ├── entropy_weight.py             # Entropy weight method for HSRI
│   └── utils/
│       ├── diagnostics.py            # VIF, Hausman, Breusch-Pagan tests
│       └── visualization.R           # ggplot2 theme & chart generation
├── outputs/
│   ├── tables/                        # Regression tables (stargazer/texreg)
│   └── figures/                       # All charts (.png, .pdf)
├── requirements.txt                   # Python dependencies
├── renv.lock                          # R environment lockfile
└── .gitignore
```

---

## How to Run

### Prerequisites

- **Python 3.10+** with packages listed in `requirements.txt`
- **R 4.2+** with `renv` for reproducible package management
- **SQL** (SQLite used for local development; scripts are dialect-agnostic)

### Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/your-username/healthcare-resilience-aging.git
cd healthcare-resilience-aging

# 2. Set up Python environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt

# 3. Set up R environment
R -e "renv::restore()"

# 4. Run the pipeline
# Step 1: Data cleaning & feature engineering
python notebooks/01_data_cleaning.ipynb
python notebooks/02_feature_engineering.ipynb

# Step 2: Panel construction
Rscript -e "rmarkdown::render('notebooks/04_panel_construction.Rmd')"

# Step 3: Modeling
Rscript src/models/twfe_model.R
Rscript src/models/heterogeneity.R
Rscript src/models/mechanism_mediation.R
Rscript src/models/robustness_checks.R

# Step 4: Generate tables & figures
Rscript src/utils/visualization.R
```

### Reproducibility Notes

- All random seeds are explicitly set at the top of each script.
- `requirements.txt` and `renv.lock` pin exact package versions.
- The processed dataset is included in `data/processed/`; raw data sources are documented in `data/dictionaries/`.

---

*Built with statistical rigor, accelerated by AI. Questions? Open an issue or reach out.*
