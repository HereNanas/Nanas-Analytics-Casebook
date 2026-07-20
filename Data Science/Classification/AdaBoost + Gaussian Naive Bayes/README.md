# 📊 AdaBoost + Gaussian Naive Bayes (Journal Quartile Classification using AdaBoost Ensemble)

Predicting a scientific journal's SCImago quartile (Q1–Q4) from its bibliometric indicators, using an AdaBoost ensemble with a Gaussian Naive Bayes base estimator.

## 🎯 Background & Motivation

Journal quartiles (Q1–Q4) are key indicators researchers use to assess a journal's relative standing within its subject category, and they directly affect publication strategy and academic credit. This project explores whether a journal's quartile can be predicted purely from its **bibliometric indicators** (citation counts, document counts, references, etc.) using an ensemble machine learning approach — without relying on subjective judgment of a journal's reputation.

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
- **Preprocessing:** missing-value check (dataset confirmed clean, 0 nulls across all columns), feature selection, and standardization via `StandardScaler`
- **Dataset** can be accessed at (see [Dataset](https://drive.google.com/drive/folders/1Kzhcqrm-NlVgzaw-5hEpodWpsdNl9p67?usp=sharing))

## ⚙️ Methodology

1. **Train-test split:** 70:30, stratified by class to preserve quartile proportions, `random_state=10`
2. **Feature scaling:** `StandardScaler` fitted **only on the training set**, then applied to both train and test sets — avoiding data leakage from the test set into preprocessing
3. **Base estimator:** `GaussianNB()`
4. **Ensemble model:** `AdaBoostClassifier(estimator=GaussianNB(), n_estimators=1000, random_state=0)`
5. **Validation strategy:** 10-fold `KFold` cross-validation, with scaling and modeling wrapped in a single `Pipeline` so that scaling is refit within each fold — preventing leakage across folds
6. **Evaluation metrics:** accuracy, precision, recall, F1-score (per class and macro-averaged), confusion matrix, ROC-AUC curve

## 📈 Results

| Metric | Score |
|---|---|
| **Test Accuracy** | **61.2%** |
| Precision (macro avg) | 0.657 |
| Recall (macro avg) | 0.624 |
| F1-score (macro avg) | 0.62 |
| Mean 10-fold CV Accuracy | 0.616 |

**Per-class performance:**

| Class | Precision | Recall | F1-score | Support |
|---|---|---|---|---|
| Q1 | 0.81 | 0.37 | 0.51 | 113 |
| Q2 | 0.48 | 0.63 | 0.54 | 113 |
| Q3 | 0.57 | 0.67 | 0.62 | 107 |
| Q4 | 0.77 | 0.82 | 0.80 | 90 |

**Confusion Matrix**

<img width="634" height="448" alt="AdaGNBKFold CM" src="https://github.com/user-attachments/assets/0fa53f03-8608-4888-98af-a137169a9497" />


**ROC-AUC Curve**

<img width="698" height="507" alt="ADAGNBKFold ROC-AUC" src="https://github.com/user-attachments/assets/b7a4f613-2ec6-4783-9f55-7c206b03995c" />


## 💡 Key Insights

- The model performs best distinguishing **Q4** (highest quartile) and reasonably well on **Q1**, both classes with more distinctive bibliometric signatures.
- **Q2 and Q3 are harder to separate** — visible in the confusion matrix as the largest source of misclassification. This makes intuitive sense: mid-tier journals often have overlapping citation and productivity profiles, making the boundary between Q2/Q3 less clear-cut than the boundary between top-tier (Q1) and lower-tier (Q4) journals.
- Q1 shows high precision (0.81) but comparatively lower recall (0.37) — meaning that when the model predicts Q1, it's usually right, but it misses a meaningful share of true Q1 journals (some get predicted as Q2).

## ⚠️ Known Limitations & Future Improvements

- **Hyperparameter tuning:** `n_estimators=1000` was set empirically rather than through systematic tuning (e.g., `GridSearchCV`). A natural next step is to sweep this and other hyperparameters properly.
- **Base estimator comparison:** results are only reported for a GaussianNB base estimator; comparing against a Decision Tree or Logistic Regression base estimator could clarify whether AdaBoost's gain comes mainly from the boosting or the base learner.
- **Class imbalance handling:** Q1–Q4 support is fairly balanced here (~90–113 samples/class), but this hasn't been stress-tested for more imbalanced quartile distributions from other subject categories.
- **Feature engineering:** currently uses raw bibliometric indicators directly; interaction terms or ratios (e.g., normalizing citation counts by subject field) could be explored.

## 🛠️ Tech Stack

Python · Pandas · NumPy · Scikit-learn · Matplotlib · Seaborn · Yellowbrick

## 📁 Repository Structure

```
AdaBoost-+-Gaussian-Naive-Bayes/
├── README.md
├── notebook/
│   └── AdaGNBKFold.ipynb
├── data/
│   └── (dataset or link to source, if shareable)
├── results/
│   ├── confusion_matrix.png
│   └── roc_auc.png
└── requirements.txt
```

## ▶️ How to Run

```bash
git clone https://github.com/HereNanas/journal-quartile-classification-adaboost.git
cd journal-quartile-classification-adaboost
pip install -r requirements.txt
jupyter notebook notebook/AdaGNBKFold.ipynb
```

## 📚 Related Publications

- Boosting and bagging classification for computer science journal ([Publication 1](https://openurl.ebsco.com/EPDB%3Agcd%3A15%3A22869903/detailv2?sid=ebsco%3Aplink%3Acrawler-gcd&id=ebsco%3Agcd%3A163298650&crl=c&jrnl=24426571&link_origin=scholar.google.com))
- Performance of Ensemble Classification for Agricultural and Biological Science Journals with Scopus Index ([Publication 2](https://distantreader.org/stacks/journals/keds/keds-37161.pdf)

## 👤 Author

**Nastiti Susetyo Fanany Putri**
[Google Scholar](https://scholar.google.com/citations?user=sLmEtkcAAAAJ&hl=id) · [GitHub](https://github.com/HereNanas)
