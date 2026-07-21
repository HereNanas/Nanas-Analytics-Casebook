# 📊 Journal Quartile Classification using Bagging Ensemble (Bagging + Decision Tree)

Predicting a scientific journal's SCImago quartile (Q1–Q4) from its bibliometric indicators, using a Bagging ensemble with a Decision Tree base estimator.

## 🎯 Background & Motivation

This project is part of a broader exploration into ensemble learning methods for predicting journal quartile classification from bibliometric indicators — complementing the AdaBoost + GaussianNB approach in [this related project](../journal-quartile-adaboost/). Comparing Bagging vs. Boosting on the same problem highlights how different ensemble strategies affect classification performance on the same dataset.

This work builds on my published research on ensemble methods for journal classification (see [Related Publications](https://scholar.google.com/citations?user=sLmEtkcAAAAJ&hl=id) below).

## 🗂️ Dataset

- **Source:** SCImago Journal Rank (SJR) indicators dataset
- **Target variable:** `SJR Best Quartile` (Q1, Q2, Q3, Q4) — 4-class classification
- **Features used (8 numeric indicators):**
  - H-index
  - Total Docs. (2020)
  - Total Docs. (3 years)
  - Total Refs.
  - Total Cites (3 years)
  - Citable Docs. (3 years)
  - Cites / Doc. (2 years)
  - Ref. / Doc.
- **Preprocessing:** feature selection and standardization via `StandardScaler`

## ⚙️ Methodology

1. **Train-test split:** 70:30, stratified by class, `random_state=10`
2. **Feature scaling:** `StandardScaler`
3. **Base estimator:** `DecisionTreeClassifier(max_depth=10)`
4. **Ensemble model:** `BaggingClassifier(estimator=DecisionTreeClassifier(max_depth=10), n_estimators=500, random_state=0)`
5. **Validation strategy:** 10-fold `KFold` cross-validation (`shuffle=True`, `random_state=1`), in addition to the held-out test set evaluation
6. **Evaluation metrics:** accuracy, precision, recall, F1-score (per class and macro-averaged), confusion matrix, ROC-AUC curve

## 📈 Results

| Metric | Score |
|---|---|
| **Test Accuracy** | **73.1%** |
| Precision (macro avg) | 0.735 |
| Recall (macro avg) | 0.729 |
| F1-score (macro avg) | 0.73 |
| Mean CV Accuracy (10-fold) | 0.736 |

**Per-class performance:**

| Class | Precision | Recall | F1-score | Support |
|---|---|---|---|---|
| Q1 | 0.80 | 0.74 | 0.77 | 525 |
| Q2 | 0.57 | 0.67 | 0.62 | 482 |
| Q3 | 0.69 | 0.63 | 0.66 | 490 |
| Q4 | 0.88 | 0.87 | 0.87 | 523 |

**Confusion Matrix**

<img width="645" height="448" alt="image" src="https://github.com/user-attachments/assets/03049f48-d192-42a2-b18f-70a600cf94e0" />

**ROC-AUC Curve**

<img width="698" height="507" alt="image" src="https://github.com/user-attachments/assets/7badefa3-8fa7-4b02-a5f9-507bc5355402" />


## 💡 Key Insights

- **Q4 is predicted most reliably** (precision 0.88, recall 0.87), followed closely by **Q1** (precision 0.80, recall 0.74) — both top and bottom quartiles have more distinctive bibliometric profiles.
- **Q2 and Q3 are the hardest to separate**, consistent with the AdaBoost project on the same dataset: the confusion matrix shows the largest misclassification block between these two middle quartiles (109 Q3 journals predicted as Q2, 70 Q2 journals predicted as Q3). Mid-tier journals likely have overlapping citation and productivity patterns.
- Q1 has a small but notable "spillover" into Q2 (134 cases), meaning a meaningful share of true Q1 journals are borderline cases the model reads as Q2.

## ⚔️ Bagging vs. Boosting: A Comparison

Since this project uses the same dataset and features as the [AdaBoost + GaussianNB project](../journal-quartile-adaboost/), it's a natural comparison point:

| Aspect | Bagging (this project) | AdaBoost (GaussianNB) |
|---|---|---|
| Base estimator | Decision Tree (max_depth=10) | Gaussian Naive Bayes |
| Ensemble strategy | Parallel, bootstrap aggregating | Sequential, adaptive reweighting |
| Test Accuracy | **73.1%** | 61.2% |
| Strengths | Captures non-linear feature interactions well via tree splits | Handles class-conditional feature distributions well |

**Takeaway:** Bagging with Decision Trees clearly outperforms AdaBoost with GaussianNB on this dataset (73.1% vs. 61.2% accuracy). A likely explanation is that Decision Trees can naturally model non-linear thresholds and interactions between bibliometric indicators (e.g. citation ratios), while GaussianNB assumes each feature is conditionally Gaussian given the class — an assumption bibliometric data may not satisfy well. This suggests that for this problem, the choice of base estimator matters more than the boosting-vs-bagging strategy itself.

## ⚠️ Known Limitations & Future Improvements

- **Feature scaling order:** `StandardScaler` is currently fit on the full dataset before the train-test split, which introduces minor data leakage. The fix is to fit the scaler on `X_train` only and apply it to both `X_train` and `X_test`.
- **Hyperparameter tuning:** `max_depth=10` and `n_estimators=500` were set empirically rather than through systematic tuning (e.g. `GridSearchCV`). Worth exploring a range of values to confirm these are close to optimal.
- **Base estimator comparison:** results are only reported for a Decision Tree base estimator; comparing against other base learners could clarify how much of the performance comes from Bagging itself vs. the base model.

## 🛠️ Tech Stack

Python · Pandas · NumPy · Scikit-learn · Matplotlib · Seaborn · Yellowbrick

## 📁 Repository Structure

```
scopus-bagging-decision-tree/
├── README.md
├── notebook/
│   └── kfold_BagDTC.ipynb
├── data/
│   └── (dataset or link to source, if shareable)
├── results/
│   ├── confusion_matrix.png
│   └── roc_auc.png
└── requirements.txt
```

## ▶️ How to Run

```bash
git clone https://github.com/HereNanas/Nanas-Analytics-Casebook.git
cd Nanas-Analytics-Casebook/data-science/classification/scopus-bagging-decision-tree
pip install -r requirements.txt
jupyter notebook notebook/kfold_BagDTC.ipynb
```

## 📚 Related Publications

- Immunology and Microbiology Journals Quartile Classification Using Decision Tree (ID3) Ensemble Models [Publication](https://www.taylorfrancis.com/chapters/edit/10.1201/9781003331674-20/immunology-microbiology-journals-quartile-classification-using-decision-tree-id3-ensemble-models-nastiti-susetyo-fanany-putri-aji-prasetya-wibawa-harits-ar-rosyid-andrew-nafalski-eisuke-hanada)

## 👤 Author

**Nastiti Susetyo Fanany Putri**
[Google Scholar](https://scholar.google.com/citations?user=sLmEtkcAAAAJ&hl=id) · [GitHub](https://github.com/HereNanas)
