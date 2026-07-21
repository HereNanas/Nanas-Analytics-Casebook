# 📊 Journal Quartile Classification: An Ensemble Methods Comparison

Predicting a scientific journal's SCImago quartile (Q1–Q4) from its bibliometric indicators, comparing six ensemble learning strategies on the same dataset, same features, and same train/test protocol for a fair, apples-to-apples evaluation.

## 🎯 Background & Motivation

Journal quartile (Q1–Q4) is a key indicator researchers use to judge a journal's relative standing within its subject category, directly affecting publication strategy and academic credit. This project asks two questions:

1. Can a journal's quartile be predicted from its bibliometric indicators alone?
2. **Which ensemble learning strategy works best for this problem** and why?

Rather than testing each method on a different slice of data (which makes comparison meaningless), every model here is trained and evaluated on the **same combined dataset spanning 27 subject domains**, using an identical preprocessing and validation pipeline. This isolates the effect of the *modeling choice* from the effect of the *data*.

This work builds on my published research on ensemble methods for journal classification (see [Related Publications](#-related-publications) below).

## 🗂️ Dataset

- **Source:** SCImago Journal Rank (SJR) indicators 2022
- **Scope:** combined dataset spanning **27 subject domains** (`28.csv`), giving a large, diverse sample rather than a single narrow category
- **Size:** 24,978 journals
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
- **Preprocessing:** missing-value check (dataset confirmed clean), feature selection, standardization via `StandardScaler` (fitted on the training set only, to avoid data leakage)

## ⚙️ Methodology (identical across all 5 models)

To keep the comparison fair, every notebook in this project follows the same protocol:

1. **Train-test split:** 70:30, stratified by class, `random_state=10`
2. **Feature scaling:** `StandardScaler` fit only on the training set, applied to both train and test sets
3. **Validation strategy:** 10-fold `KFold` cross-validation (`shuffle=True`, `random_state=1`), with scaling and modeling wrapped in a single `Pipeline` so scaling is refit within each fold
4. **Evaluation metrics:** accuracy, precision, recall, F1-score (per class and macro-averaged), confusion matrix, ROC-AUC curve

## 🧪 Models Compared

| # | Notebook | Ensemble Strategy | Base Estimator |
|---|---|---|---|
| 1 | `01_adaboost_gaussiannb.ipynb` | AdaBoost (sequential, adaptive reweighting) | Gaussian Naive Bayes |
| 2 | `02_adaboost_decisiontree.ipynb` | AdaBoost | Decision Tree |
| 3 | `03_bagging_gaussiannb.ipynb` | Bagging (parallel, bootstrap aggregating) | Gaussian Naive Bayes |
| 4 | `04_bagging_decisiontree.ipynb` | Bagging | Decision Tree |
| 5 | `05_xgboost_decisiontree.ipynb` | Gradient Boosting (XGBoost) | Trees (`booster='gbtree'`, XGBoost's native/default base learner) |
| 6 | `06_xgboost_gnb_augmented.ipynb` | Gradient Boosting (XGBoost) | Trees, with GaussianNB predicted probabilities added as extra engineered features |

## 📈 Results

| Method | Base Estimator | Test Accuracy | Macro Precision | Macro Recall | Macro F1 | Mean CV Accuracy |
|---|---|---|---|---|---|---|
| AdaBoost | Gaussian Naive Bayes | 45.1% | 0.49 | 0.48 | 0.44 | 45.5% |
| AdaBoost | Decision Tree | 51.7% | 0.51 | 0.51 | 0.51 | 52.0% |
| Bagging | Gaussian Naive Bayes | 41.5% | 0.47 | 0.44 | 0.40 | 42.4% |
| **Bagging** | **Decision Tree** | **59.2%** | **0.59** | **0.60** | **0.59** | **59.3%** |
| XGBoost | Trees (native, `gbtree`) | 56.4% | 0.56 | 0.56 | 0.56 | 57.2% |
| XGBoost | Trees + GNB probability features | 56.5% | 0.56 | 0.56 | 0.56 | 56.8% |

🏆 **Best performer: Bagging + Decision Tree (59.2% accuracy)** — both XGBoost variants land close behind (~56%), essentially tied with each other.

**Per-class F1-score comparison:**

| Model | Q1 | Q2 | Q3 | Q4 |
|---|---|---|---|---|
| AdaBoost + GaussianNB | 0.52 | 0.26 | 0.36 | 0.62 |
| AdaBoost + Decision Tree | 0.63 | 0.40 | 0.43 | 0.59 |
| Bagging + GaussianNB | 0.43 | 0.24 | 0.33 | 0.60 |
| **Bagging + Decision Tree** | **0.71** | **0.48** | **0.49** | **0.67** |
| XGBoost (native trees) | 0.69 | 0.45 | 0.45 | 0.64 |
| XGBoost + GNB features | 0.69 | 0.46 | 0.46 | 0.64 |

**Confusion Matrices & ROC-AUC Curves**

[Bagging + Decision Tree Confusion Matrix]
<img width="654" height="448" alt="image" src="https://github.com/user-attachments/assets/d0cdc654-80c0-46aa-9b3c-2bf8dd346d35" />
<img width="698" height="507" alt="image" src="https://github.com/user-attachments/assets/62f40a4b-f05c-4ea0-a3fb-e1993e2d408a" />

*(See `results/` folder for confusion matrices and ROC-AUC curves for all 5 models)*

## 💡 Key Insights

- **Base estimator choice matters more than ensemble strategy.** Across both AdaBoost and Bagging, swapping Gaussian Naive Bayes for a Decision Tree consistently improved performance (AdaBoost: 45.1% → 51.7%; Bagging: 41.5% → 59.2%). This suggests GaussianNB's assumption that each feature is conditionally Gaussian given the class doesn't hold well for bibliometric data, while Decision Trees can capture non-linear thresholds and interactions naturally.
- **Bagging outperforms Boosting here when paired with Decision Trees** (59.2% vs. 51.7%). A likely explanation: bibliometric features are fairly noisy, and Bagging's variance-reduction through bootstrap aggregation may generalize better than AdaBoost's adaptive reweighting, which can overfit to hard-to-classify borderline cases (especially between adjacent quartiles).
- **XGBoost is competitive but doesn't beat simple Bagging + Decision Tree** on this dataset (~56% vs 59.2%) — a reminder that more sophisticated algorithms don't automatically win; the right combination of a simpler method with a well-matched base estimator can outperform more complex boosting frameworks.
- **Augmenting XGBoost with GaussianNB probability features adds essentially no value** (56.4% → 56.5%, within noise). This is a useful negative result: it suggests the original 8 bibliometric features already capture most of the signal GaussianNB can extract, so stacking its output on top of XGBoost doesn't provide meaningfully new information.
- **Q2 and Q3 are consistently the hardest classes across every model** (lowest F1-scores throughout), while **Q1 and Q4 are easiest**. This pattern holds regardless of algorithm, pointing to a genuine characteristic of the data: mid-tier journals (Q2/Q3) have more overlapping bibliometric profiles than top- or bottom-tier journals.

## ⚠️ Known Limitations & Future Improvements

- **Hyperparameters were set empirically**, not through systematic tuning (e.g. `GridSearchCV`) — e.g. Decision Tree `max_depth` varies between notebooks (10 for Bagging, 1000 for AdaBoost) without a controlled sweep. A fairer comparison would tune each model's hyperparameters independently before comparing final performance.
- **XGBoost cannot use a custom base estimator** the way AdaBoost/Bagging can in scikit-learn. `05_xgboost_decisiontree.ipynb` explicitly sets `booster='gbtree'` to confirm XGBoost is already using trees as its native weak learner (its only real option); `06_xgboost_gnb_augmented.ipynb` instead incorporates GaussianNB via **feature augmentation** (its predicted class probabilities added as extra input features) rather than as a true "base estimator" swap — a legitimate but different technique from what AdaBoost/Bagging do.
- **Test set sizes differ slightly across notebooks** (7,494 samples in the scikit-learn-based notebooks vs. 7,413 in the two XGBoost notebooks), due to a minor difference in how missing values were handled during data loading (`decimal=','` parsing plus `dropna()` in the XGBoost notebooks). This doesn't materially change the conclusions, but exact accuracy figures aren't perfectly apples-to-apples with the other four models.
- **Class imbalance:** Q1–Q4 support is reasonably balanced in this dataset (~1,500–2,300 per class in the test set), but this hasn't been tested on more imbalanced subject categories.
- **Single train-test split:** while 10-fold CV is used for validation, the headline test-set numbers come from one stratified split; repeating with multiple random seeds would give more robust accuracy estimates.
- **Deprecated XGBoost parameter:** both XGBoost notebooks pass `use_label_encoder=False`, which triggers a harmless but noisy deprecation warning in recent `xgboost` versions (the parameter no longer has any effect and can simply be removed).

## 🛠️ Tech Stack

Python · Pandas · NumPy · Scikit-learn · XGBoost · Matplotlib · Seaborn · Yellowbrick

## 📁 Repository Structure

```
journal-quartile-ensemble-comparison/
├── README.md
├── notebook/
│   ├── 01_adaboost_gaussiannb.ipynb
│   ├── 02_adaboost_decisiontree.ipynb
│   ├── 03_bagging_gaussiannb.ipynb
│   ├── 04_bagging_decisiontree.ipynb
│   ├── 05_xgboost_decisiontree.ipynb
│   └── 06_xgboost_gnb_augmented.ipynb
├── data/
│   └── 28.csv  (or a link to the source, if the raw file isn't shareable)
├── results/
│   ├── confusion_matrix_adaboost_gaussiannb.png
│   ├── confusion_matrix_adaboost_decisiontree.png
│   ├── confusion_matrix_bagging_gaussiannb.png
│   ├── confusion_matrix_bagging_decisiontree.png
│   ├── confusion_matrix_xgboost_decisiontree.png
│   ├── confusion_matrix_xgboost_gnb_augmented.png
│   ├── roc_auc_adaboost_gaussiannb.png
│   ├── roc_auc_adaboost_decisiontree.png
│   ├── roc_auc_bagging_gaussiannb.png
│   ├── roc_auc_bagging_decisiontree.png
│   ├── roc_auc_xgboost_decisiontree.png
│   └── roc_auc_xgboost_gnb_augmented.png
└── requirements.txt
```

## ▶️ How to Run

```bash
git clone https://github.com/HereNanas/Nanas-Analytics-Casebook.git
cd Nanas-Analytics-Casebook/data-science/classification/journal-quartile-ensemble-comparison
pip install -r requirements.txt
jupyter notebook notebook/04_bagging_decisiontree.ipynb   # or any of the 5 notebooks
```

## 📚 Related Publications

- Boosting and bagging classification for a computer science journal [publication 1](https://openurl.ebsco.com/EPDB%3Agcd%3A15%3A22869903/detailv2?sid=ebsco%3Aplink%3Acrawler-gcd&id=ebsco%3Agcd%3A163298650&crl=c&jrnl=24426571&link_origin=scholar.google.com)
- Classification of Engineering Journals Quartile using Various Supervised Learning Models [Publication 2](https://pdfs.semanticscholar.org/d763/a9504977ca0e1938b09240bffab4fcbc2f12.pdf)
- Performance of Ensemble Classification for Agricultural and Biological Science Journals with Scopus Index [Publication 3](https://distantreader.org/stacks/journals/keds/keds-37161.pdf)
- Immunology and Microbiology Journals Quartile Classification Using Decision Tree (ID3) Ensemble Models [Publication 4](https://www.taylorfrancis.com/chapters/edit/10.1201/9781003331674-20/immunology-microbiology-journals-quartile-classification-using-decision-tree-id3-ensemble-models-nastiti-susetyo-fanany-putri-aji-prasetya-wibawa-harits-ar-rosyid-andrew-nafalski-eisuke-hanada)

## 👤 Author

**Nastiti Susetyo Fanany Putri**
[Google Scholar](https://scholar.google.com/citations?view_op=list_works&hl=id&hl=id&user=sLmEtkcAAAAJ) · [GitHub](https://github.com/HereNanas)
