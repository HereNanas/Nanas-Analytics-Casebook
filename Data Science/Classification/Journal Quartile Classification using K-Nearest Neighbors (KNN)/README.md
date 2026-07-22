# 📊 Journal Quartile Classification using K-Nearest Neighbors (KNN)

Predicting a scientific journal's SCImago quartile (Q1–Q4) from its bibliometric indicators, using a K-Nearest Neighbors classifier with a systematically selected value of K.

## 🎯 Background & Motivation

This project explores a simpler, non-ensemble approach to journal quartile classification — K-Nearest Neighbors — as a complement to the ensemble methods explored in the [Journal Quartile Ensemble Comparison](../journal-quartile-ensemble-comparison/) project. Rather than combining multiple learners, KNN classifies each journal based on the quartiles of its closest neighbors in feature space, offering an interpretable baseline for comparison.

## 🗂️ Dataset

- **Source:** SCImago Journal Rank (SJR) indicators
- **Scope:** Computer Science journals
- **Size:** 1,640 journals, 20 original columns
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
- **Preprocessing:** missing-value check (33 missing `Publisher` values found, not used as a feature so no impact on modeling), EDA via pairplot and correlation matrix, feature selection, standardization via `StandardScaler`

## ⚙️ Methodology

1. **Train-test split:** 80:20, stratified by class, `random_state=0`
2. **Feature scaling:** `StandardScaler` fitted only on the training set, then applied to both train and test sets
3. **Model selection (K):**
   - Evaluated error rate on the test set across `K=3` to `K=74`, selecting `K=47` from the elbow of the error curve
   - Separately ran 10-fold cross-validation across the same range of K values, which identified `K=32` as the statistically optimal value
4. **Final models:** both K values were evaluated for comparison:
   - `KNeighborsClassifier(n_neighbors=47)` — from the elbow method
   - `KNeighborsClassifier(n_neighbors=32)` — from cross-validation (used as the primary reported result)
5. **Evaluation metrics:** accuracy, precision, recall, F1-score (per class and macro-averaged), confusion matrix

## 📈 Results

| Model | Test Accuracy | Precision (macro) | Recall (macro) | F1 (macro) |
|---|---|---|---|---|
| **K=47 (elbow method, primary result)** | **64.9%** | **0.66** | **0.65** | **0.65** |
| K=24 (cross-validation) | 63.1% | 0.64 | 0.64 | 0.64 |

**Per-class performance (K=47):**

| Class | Precision | Recall | F1-score | Support |
|---|---|---|---|---|
| Q1 | 0.85 | 0.69 | 0.76 | 89 |
| Q2 | 0.53 | 0.65 | 0.58 | 83 |
| Q3 | 0.54 | 0.50 | 0.52 | 84 |
| Q4 | 0.74 | 0.78 | 0.76 | 72 |

**Confusion Matrix (K=47)**

<img width="636" height="448" alt="image" src="https://github.com/user-attachments/assets/152b798e-8d5b-400a-b9aa-af6f5cb01f63" />


**K Selection Curves**

<img width="576" height="455" alt="image" src="https://github.com/user-attachments/assets/6e9ca891-a5bb-44b3-88c6-8060bdce2aeb" />
<img width="1634" height="1252" alt="image" src="https://github.com/user-attachments/assets/be4210c8-2a50-4798-abc2-da10cb293645" />


## 💡 Key Insights

- **Q1 remains the most reliably classified** (precision 0.85 at K=47), consistent with the pattern seen across other classification projects in this casebook; top-quartile journals tend to have more distinctive bibliometric profiles.
- **Q3 is the weakest class** (F1 0.52), with the confusion matrix showing meaningful overlap with both Q2 and Q4 — reinforcing the recurring finding across this casebook that mid-tier quartiles are inherently harder to separate than top or bottom-tier ones.
- **Fixing the feature encoding improved accuracy substantially** (from ~57% to ~65%), confirming that preserving true numeric magnitude rather than converting bibliometric features to ordinal ranks matters a great deal for a distance-based algorithm like KNN.
- **Interestingly, the simpler elbow-method K (47) now outperforms the cross-validation-selected K (24)** (64.9% vs 63.1%), the reverse of what was observed before the encoding fix. This suggests the error surface across this K range is fairly flat, and/or that CV-based K selection can be sensitive to how clean the underlying features are. A systematic `GridSearchCV` sweep would be a more conclusive way to settle this.
- This project uses the same **Computer Science** journal category as my published paper on ensemble classification for computer science journals (see [Related Publications](#-related-publications)) — a useful direct comparison point between a simple non-ensemble method (KNN) and ensemble approaches on the same domain.

## ⚠️ Known Limitations & Future Improvements

- **Hyperparameter tuning is limited to K**  other KNN parameters (e.g., distance metric, weighting scheme) were not explored. A systematic `GridSearchCV` over both K and distance metric could help resolve the elbow-vs-CV discrepancy noted above.
- **Minor leftover:** an unused `ks_2samp` import (`scipy.stats.stats`) remains in the notebook and triggers a harmless deprecation warning — safe to remove.

## 🛠️ Tech Stack

Python · Pandas · NumPy · Scikit-learn · Matplotlib · Seaborn

## 📁 Repository Structure

```
knn-journal-classification/
├── README.md
├── notebook/
│   └── KNN_Classification.ipynb
├── data/
│   └── Computer Science.csv  
├── results/
│   ├── confusion_matrix.png
│   ├── k_elbow_curve.png
│   └── cv_k_selection.png
└── requirements.txt
```

## ▶️ How to Run

```bash
git clone https://github.com/HereNanas/Nanas-Analytics-Casebook.git
cd Nanas-Analytics-Casebook/data-science/classification/knn-journal-classification
pip install -r requirements.txt
jupyter notebook notebook/KNN_Classification.ipynb
```

## 📚 Related Publications

- Classification of Engineering Journals Quartile using Various Supervised Learning Models [Publication](https://pdfs.semanticscholar.org/d763/a9504977ca0e1938b09240bffab4fcbc2f12.pdf)
## 👤 Author

**Nastiti Susetyo Fanany Putri**
[Google Scholar](https://scholar.google.com/citations?view_op=list_works&hl=id&hl=id&user=sLmEtkcAAAAJ) · [GitHub](https://github.com/HereNanas)
