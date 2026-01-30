
# Global Happiness Scores & Factors (2015 vs 2020) — Statistical Analysis

This project analyzes cross-national happiness data to understand **(1) regional differences in happiness in 2020**, **(2) changes in happiness between 2015 and 2020 (pre/post COVID period)**, and **(3) which socio-economic factors are most strongly associated with national happiness in 2020**.

The analysis emphasizes **rigorous statistical workflow**: exploratory analysis, assumption checks, hypothesis testing, and model diagnostics. Results are interpreted with clear limits on causal inference.

---

## Data Sources

**Primary source (original):** World Happiness Report, based on the **Gallup World Poll** (≈1,000 adults surveyed per country per year; 160+ countries; consistent methodology to allow cross-country/time comparisons).  
**Dataset used (curated):** Kaggle — *World Happiness Report (till 2023)* by **Sazidthe1 (2023)**.  
- Kaggle dataset: https://www.kaggle.com/datasets/sazidthe1/global-happiness-scores-and-factors  
- Original report portal: https://worldhappiness.report/

The dataset integrates explanatory variables such as GDP per capita, healthy life expectancy, social support, freedom, generosity, and corruption perception, using sources like the World Bank, WHO, and Gallup. When recent values are missing, the report sometimes uses prior values or careful imputation for analysis; however, official rankings rely only on observed survey-based data.

---

## Dataset Description

### World Happiness Report 2020
- **153 observations** (countries)  
- **9 columns**

### World Happiness Report 2015
- **158 observations** (countries)  
- **9 columns**

Common columns (both years):
1. `country` (categorical)  
2. `region` (categorical)  
3. `happiness_score` (quantitative; 0–10 Cantril ladder)  
4. `gdp_per_capita` (quantitative; log PPP-adjusted GDP per capita)  
5. `social_support` (quantitative proportion; 0–1)  
6. `healthy_life_expectancy` (quantitative; years)  
7. `freedom_to_make_life_choices` (quantitative proportion; 0–1)  
8. `generosity` (quantitative index; residualized donation behavior vs GDP)  
9. `perceptions_of_corruption` (quantitative proportion; 0–1)

### Variable Glossary (Compact)
| Variable | Meaning | Type / Unit |
|---|---|---|
| `happiness_score` | Average life evaluation (Cantril ladder) | 0–10 score |
| `gdp_per_capita` | log GDP per capita (PPP) | log(USD) |
| `social_support` | Has someone to rely on | proportion (0–1) |
| `healthy_life_expectancy` | Health-adjusted life expectancy | years |
| `freedom_to_make_life_choices` | Satisfaction with freedom | proportion (0–1) |
| `generosity` | Prosocial behavior index (residual) | index |
| `perceptions_of_corruption` | Corruption perceived widespread | proportion (0–1) |

---

## Research Questions

**Q1.** Do mean happiness scores differ significantly across regions/continents in **2020**?  
**Q2.** Did happiness scores change between **2015** and **2020** (paired country comparison; pre/post COVID-era shift)?  
**Q3.** Which explanatory variables are most strongly associated with happiness in **2020**, and how much variance can they explain?

---

## Methods & Statistical Procedures (Technical)

### EDA (All Questions)
- Descriptive statistics (mean/median/IQR) and outlier screening
- Boxplots and histograms to inspect distributional shape and between-group differences

### Assumption Checks (Formal)
- **Normality:** Shapiro–Wilk test + Q–Q plots  
- **Equal variances (ANOVA):** Levene’s test  
- **Regression diagnostics:** residuals vs fitted (linearity + homoscedasticity), Q–Q plot of residuals (normality)

### Q1: Regional Differences (2020)
- **One-way ANOVA** to compare mean `happiness_score` across regions  
  - Null: all regional means equal  
  - Alternative: at least one regional mean differs  
- Supporting diagnostics:
  - Normality of outcome distribution (Q–Q, Shapiro–Wilk)
  - Homoscedasticity (Levene’s)

### Q2: Change Over Time (2015 vs 2020)
- Countries with data available in both years are paired (matched by `country`)
- **Paired t-test**
  - Tests whether mean difference in happiness scores between 2020 and 2015 is zero  
- Diagnostics:
  - Q–Q plots for each year + Q–Q plot of paired differences
  - Histogram checks for symmetry
  - Shapiro–Wilk on paired differences (normality)

### Q3: Drivers of Happiness (2020)
- Multiple Linear Regression with candidate predictors:
  - `gdp_per_capita`, `social_support`, `healthy_life_expectancy`, `freedom_to_make_life_choices`, `generosity`, `perceptions_of_corruption`
- Model building strategy:
  1. Fit **SLR** (simple linear regression) for each predictor to understand marginal association
  2. Fit combinations from 2 up to 6 predictors
  3. Select model based on interpretability + fit + parsimony  
- Model selection cross-check:
  - **AIC-based stepwise selection** using `step()` (confirmatory)
- Multicollinearity:
  - **VIF** (Variance Inflation Factor); rule of thumb: VIF < 5 indicates acceptable collinearity
- Model evaluation:
  - Adjusted R², overall F-test, coefficient significance

---

## Key Results (Reported)

### Q1 — ANOVA (2020 Regional Differences)
- Median happiness score increases roughly from **Sub-Saharan Africa / South Asia → Western Europe / North America**.
- Normality check: Shapiro–Wilk p-value reported as **0.1635**, consistent with approximate normality.
- Levene’s test supports equal variances across groups.
- ANOVA comparisons: mean happiness in **North America** was not significantly different from most regions;  
  the only moderate similarity highlighted was **North America vs Western Europe** (reported p ≈ **0.48**), suggesting comparable average happiness in those two regions.

> Interpretation: happiness differs meaningfully by region overall, and the highest regions appear clustered (e.g., North America and Western Europe).

### Q2 — Paired t-test (2015 vs 2020)
- Paired t-test on **149 countries** with both years available:
  - **t = -2.34**, **p = 0.021**
  - Mean difference (2020 − 2015) = **-0.112**
  - 95% CI: **[-0.207, -0.017]** (does not include 0)

> Interpretation: the average happiness score in **2020 was significantly lower than 2015** in the matched-country sample.

### Q3 — Multiple Regression (2020)
- A 5-variable model excluding **generosity** was selected as best (generosity was often statistically insignificant).
- Stepwise AIC (`step()`) selected the same 5-variable model.
- Model performance:
  - Adjusted R² ≈ **0.737**
  - Overall model: **F = 86.25**, **p < 0.0001**
  - All five included variables were statistically significant.

> Interpretation: a small set of socio-economic and institutional factors explains a large proportion of cross-country variation in happiness in 2020, with generosity contributing limited incremental explanatory power in this specification.

---

## Figures / Report Snapshot

If you have a compiled report or screenshot summary, you can include it in the repo and link it here:

![Report Snapshot](report_snapshot.png)

> Replace `report_snapshot.png` with your actual file name (or delete this section if not needed).

---

## Scope of Inference (Important)
This dataset supports **cross-national descriptive comparisons** and **associational modeling**, but:

1. **Correlation ≠ causation:** countries were not randomly assigned to GDP, social support, etc., so causal claims cannot be made.
2. **Limited generalizability:** results apply to the countries included; the set of countries is not itself a random sample from “all possible countries.”
3. **Measurement & imputation caveats:** some variables may include estimated values for analysis when recent measurements are missing.

---

## Deliverables
- Clean dataset(s) for 2015 and 2020
- EDA plots (boxplots, histograms, Q–Q)
- Statistical test outputs (ANOVA, paired t-test)
- Regression models + diagnostics (residual plots, VIF, model selection)
- Final written summary with inference limits

---
