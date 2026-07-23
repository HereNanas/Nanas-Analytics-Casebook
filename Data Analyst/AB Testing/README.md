# 🧪 A/B Testing: E-Commerce "Add to Cart" Button Color Experiment

Evaluating whether changing the "Add to Cart" button color (Variant B: green) increases purchase conversion and revenue compared to the current design (Variant A: control), using a rigorous statistical testing framework.

## 🎯 Business Problem

An e-commerce product team wants to know whether a UI change, the "Add to Cart" button color, meaningfully improves purchase conversion. Before rolling the change out to 100% of traffic, the team needs statistically sound evidence (not just "Group B looks higher") that the difference is real and large enough to matter for the business.

## 🗂️ Experiment Design

- **Sample size:** 10,000 users, randomly split ~50:50 into Group A (control) and Group B (variant)
- **Duration:** 30-day window
- **Metrics tracked:** click-through on the button, purchase conversion, and revenue per user
- **Randomization check:** Sample Ratio Mismatch (SRM) test via chi-square — confirms the 50:50 split was not distorted

## ⚙️ Analytical Approach

1. **Sample Ratio Mismatch (SRM) check**: chi-square goodness-of-fit test to confirm random assignment wasn't broken before trusting any downstream result
2. **Exploratory analysis**: conversion rate, click rate, and average revenue by group, with 95% confidence interval visualization
3. **Hypothesis test on conversion rate**: one-tailed two-proportion Z-test (`H0: conversion_B ≤ conversion_A` vs. `H1: conversion_B > conversion_A`)
4. **Hypothesis test on revenue**: one-tailed Mann-Whitney U test (non-parametric, appropriate since revenue is highly right-skewed with many zero values from non-purchasers)
5. **Effect size & practical significance**: absolute lift, relative lift, and a 95% confidence interval for the difference in conversion rates, to judge whether the effect is not just *statistically* significant but *practically* meaningful

## 📈 Results

| Check | Result |
|---|---|
| SRM test | p = 1.0 — no sample ratio mismatch, split is clean |
| Group A conversion rate | 9.87% (n = 5,076) |
| Group B conversion rate | 13.59% (n = 4,924) |
| Absolute lift (B − A) | +3.72 percentage points |
| Relative lift | +37.65% |
| 95% CI for lift (B − A) | [2.46%, 4.98%] |
| **Conversion rate test (Z-test, one-tailed)** | **p = 3.7 × 10⁻⁹** — highly significant |
| **Revenue test (Mann-Whitney U, one-tailed)** | **p = 1.0 × 10⁻⁸** — highly significant |

Both the conversion rate and the revenue improvement for Group B are **statistically significant at any conventional threshold**, and the 95% confidence interval for the lift excludes zero entirely — confirming the effect is real, not noise.

**Visualizations**

<img width="576" height="455" alt="image" src="https://github.com/user-attachments/assets/b7749ec4-5f11-446c-b198-e0f49afdad12" />

<img width="1189" height="490" alt="image" src="https://github.com/user-attachments/assets/f0a5152e-f62b-49e8-be04-a50074e979b5" />


## 💡 Key Insights

- **The effect is both statistically and practically significant.** A 37.65% relative lift in conversion rate is a substantial business impact, not a marginal or borderline result; this isn't a case of "technically significant but practically meaningless."
- **Revenue confirms the same story as conversion.** Using a Mann-Whitney U test (appropriate for the skewed revenue distribution) rather than a simple mean comparison avoids being misled by outliers, and still shows a big, significant difference in the same direction.
- **The SRM check mattered.** Confirming p = 1.0 on the randomization split before interpreting any other result is a standard but often-skipped safeguard — it rules out a broken experiment setup as an alternative explanation for the observed difference.

## 🎯 Recommendation

**✅ Ship the green "Add to Cart" button (Variant B) to 100% of traffic.** The evidence clearly supports it: the lift is large, the confidence interval is tight and entirely positive, and both the primary metric (conversion) and secondary metric (revenue) point in the same direction.

## 🛠️ Tech Stack

Python · Pandas · NumPy · SciPy · Statsmodels · Matplotlib · Seaborn

## 📁 Repository Structure

```
AB Testing/
├── README.md
├── notebook/
│   └── AB Testing.ipynb
├── data/
│   └── ab_test_ecommerce.csv
├── results/
│   ├── conversion_rate_by_group.png
│   └── revenue_distribution.png
└── requirements.txt
```

## ▶️ How to Run

```bash
git clone https://github.com/HereNanas/Nanas-Analytics-Casebook.git
cd Nanas-Analytics-Casebook/data-analyst/ab-testing-ecommerce-button
pip install -r requirements.txt
jupyter notebook notebook/AB_Testing.ipynb
```

## 👤 Author

**Nastiti Susetyo Fanany Putri**
[Google Scholar](https://scholar.google.com/citations?view_op=list_works&hl=id&hl=id&user=sLmEtkcAAAAJ) · [GitHub](https://github.com/HereNanas)
