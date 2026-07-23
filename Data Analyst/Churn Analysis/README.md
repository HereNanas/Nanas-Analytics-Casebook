# 📉 SaaS Customer Churn Prediction

Predicting which SaaS customers are likely to churn, using logistic regression and random forest classifiers, with a focus on identifying actionable churn drivers rather than just maximizing accuracy.

## 🎯 Business Problem

Customer churn is one of the most expensive problems for a subscription (SaaS) business — acquiring a new customer typically costs far more than retaining an existing one. This project asks:

1. Can we predict which customers are likely to churn *before* they leave?
2. **Which factors actually drive churn**, so the business can act on them (e.g. contract incentives, proactive support outreach) rather than just get a black-box prediction?

## 🗂️ Dataset

- **Type:** synthetic SaaS customer dataset (5,000 customers), generated with an explicit, documented business-rule-based churn logic (not random noise) — this makes the "ground truth" drivers of churn known and interpretable, useful for validating whether the models correctly recover known patterns
- **Overall churn rate:** 30.4%
- **Features:**
  - `tenure_months`, `monthly_charges`, `total_charges`
  - `contract_type` (Month-to-month / One Year / Two Year), `payment_method`
  - `has_tech_support`, `has_online_security` (binary)
  - `num_support_tickets`, `avg_monthly_usage`
- **Target:** `churn` (binary)

**Business rules encoded in the churn-generating logic** (for reference/validation): month-to-month contracts and electronic check payments increase risk; having neither tech support nor online security increases risk; 3+ support tickets increases risk further, 5+ increases it more; tenure under 6 months is high-risk, tenure over 36 months is protective; high monthly charges combined with low usage (dissatisfaction signal) increases risk.

## ⚙️ Methodology

1. **EDA** — churn rate by contract type, tenure distribution split by churn status
2. **Feature engineering** — `tenure_group` (New/Medium/Long) for exploratory visualization (not used as a model input, to avoid duplicating `tenure_months`)
3. **Train-test split:** 70:30, stratified by churn status, `random_state=42`
4. **Preprocessing pipeline:** `StandardScaler` for numeric features, `OneHotEncoder` for categorical features, wrapped in a `ColumnTransformer` + `Pipeline` (fit only on the training set — no leakage)
5. **Models:** Logistic Regression and Random Forest, both with `class_weight='balanced'` to address the 70:30 class imbalance
6. **Evaluation:** precision, recall, F1 (per class), accuracy, AUC-ROC — with particular attention to **recall on the Churn class**, since missing an actual churner is the costlier business error
7. **Interpretation:** odds ratios extracted from the Logistic Regression coefficients to identify actionable churn drivers

## 📈 Results

| Model | Accuracy | Churn Precision | Churn Recall | Churn F1 | AUC-ROC |
|---|---|---|---|---|---|
| **Logistic Regression** | 66.1% | 0.46 | **0.664** | **0.543** | **0.726** |
| Random Forest | 70.5% | 0.53 | 0.29 | 0.372 | 0.696 |

**Top churn drivers (odds ratios, Logistic Regression):**

| Feature | Odds Ratio | Interpretation |
|---|---|---|
| Payment: Electronic check | 1.92 | Nearly 2× higher churn odds vs. baseline payment method |
| Number of support tickets | 1.46 | More complaints → substantially higher churn risk |
| Contract: Two Year | 0.16 | Strongest protective factor — long contracts dramatically reduce churn |
| Contract: One Year | 0.35 | Also strongly protective, though less than Two Year |
| Tenure (months) | 0.67 | Longer-tenured customers are meaningfully less likely to churn |
| Has tech support | 0.75 | Modestly protective |
| Has online security | 0.74 | Modestly protective |

**Visualizations**

<img width="1389" height="490" alt="image" src="https://github.com/user-attachments/assets/54129f03-0b77-4ba9-a5b6-957d16cf9188" />
<img width="720" height="600" alt="roc_curve_comparison" src="https://github.com/user-attachments/assets/d8d1ce07-03c3-4a8b-89cb-0cfd5488848c" />


## 💡 Key Insights

- **Random Forest's higher accuracy is misleading here — this is a textbook "accuracy paradox."** RF misses 71% of actual churners (recall 0.287), while Logistic Regression catches 66% of them (recall 0.664). For a churn use case, missing a real churner is far more costly than a false alarm, so **Logistic Regression is the more business-appropriate model**, despite its lower headline accuracy.
- **Contract length is by far the strongest lever available to the business.** Two-year contracts cut churn odds to roughly 1/6th of the baseline; this points directly to a retention strategy: incentivize longer commitments (discounts for annual/biennial upgrades).
- **Electronic check payment is a strong, actionable red flag.** Customers paying this way have nearly double the churn odds, likely correlated with lower payment friction/commitment. Prompting a switch to automatic payment methods (credit card, bank transfer) is a concrete, testable retention intervention.
- **Support ticket volume is a leading indicator, not just a lagging one.** Customers with more tickets are meaningfully more likely to churn; proactive outreach after the 3rd support ticket could be a useful early-warning trigger.

## 🎯 Recommendations

1. **Prioritize contract-length incentives** — offer meaningful discounts or added features for customers who upgrade from month-to-month to annual/biennial plans.
2. **Nudge Electronic check payers toward automatic payment methods** (credit card, bank transfer) via a small incentive, and monitor whether this reduces churn.
3. **Trigger a proactive retention touchpoint after a customer's 3rd support ticket**, rather than waiting for a churn signal to appear elsewhere.
4. **Use Logistic Regression (not Random Forest) as the production churn-scoring model**, given its substantially better recall on the class that matters most for retention action.

## ⚠️ Known Limitations & Corrections Made

- **Random Forest's poor churn recall wasn't fully resolved by `class_weight='balanced'` alone.** Future work could explore oversampling techniques (e.g., SMOTE), threshold tuning (lowering the classification threshold to favor recall), or hyperparameter tuning via `GridSearchCV`.
- **This is a synthetic dataset** with a known, documented generating process — useful for demonstrating the modeling and interpretation workflow, but real-world churn drivers would need to be validated against actual customer data.

## 🛠️ Tech Stack

Python · Pandas · NumPy · Scikit-learn · Matplotlib · Seaborn

## 📁 Repository Structure

```
Churn Analysis/
├── README.md
├── Churn Analysis.ipynb
├── saas_churn_data.csv/
├── eda_contract_tenure.png
├── roc_curve_comparison.png
└── requirements.txt
```

## ▶️ How to Run

```bash
git clone https://github.com/HereNanas/Nanas-Analytics-Casebook.git
cd Nanas-Analytics-Casebook/data-analyst/saas-churn-prediction
pip install -r requirements.txt
jupyter notebook notebook/Churn_Analysis.ipynb
```

## 👤 Author

**Nastiti Susetyo Fanany Putri**
[Google Scholar](https://scholar.google.com/citations?view_op=list_works&hl=id&hl=id&user=sLmEtkcAAAAJ) · [GitHub](https://github.com/HereNanas)
