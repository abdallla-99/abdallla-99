# Predicting dropout from maternal care in Nigeria with machine learning (2024 NDHS)

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24247128.svg)](https://doi.org/10.5281/zenodo.24247128)
![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-Apache%202.0-blue)

This repository holds the code and summary results for a study that predicts which Nigerian mothers drop out of three steps in maternal care: **antenatal care (ANC)**, **facility delivery** and **maternal postnatal care (PNC)**. The study uses the 2024 Nigeria Demographic and Health Survey (NDHS).

Eight machine-learning models are compared. Evaluation respects the survey design: test sets are split by **primary sampling unit (PSU)**, and performance is reported with **DHS sampling weights**. Models are explained with **SHAP** and **weighted permutation importance**.

## Key results

Best model per outcome, scored with DHS survey weights. Each figure is the mean of 5 PSU-grouped 80/20 holdout splits.

| Outcome | Best model | ROC-AUC (SD) | PR-AUC | Sensitivity | F1 |
| --- | --- | --- | --- | --- | --- |
| ANC dropout | Gradient Boosting | 0.805 (0.020) | 0.586 | 0.794 | 0.608 |
| Facility-delivery dropout | Logistic Regression | 0.865 (0.009) | 0.889 | 0.846 | 0.832 |
| Maternal PNC dropout | Gradient Boosting | 0.817 (0.013) | 0.836 | 0.783 | 0.781 |

The full comparison of all eight models, weighted and unweighted, is in [`model_comparison_summary.csv`](model_comparison_summary.csv).

**Main predictors:** for all three outcomes, the strongest predictors by mean |SHAP| are **mother's education**, **region** and **household wealth**. Weighted permutation importance gives the same top two (education and region). Details are in [`shap_importance_summary.csv`](shap_importance_summary.csv) and [`weighted_permutation_importance.csv`](weighted_permutation_importance.csv).

## Methods at a glance

| Step | What the code does |
| --- | --- |
| Data | 2024 NDHS Children's Recode (KR), most recent birth only (`bidx == 1`) |
| Outcomes | ANC dropout = no ANC visits (`m14`); facility-delivery dropout = home or other non-facility birth (`m15`); maternal PNC dropout = no maternal check before or after discharge (`m62`, `m66`) |
| Predictors | 19 maternal, household and contextual features (education, region, wealth, birth interval, media exposure, water, sanitation, fuel and others) |
| Models | Logistic Regression, Decision Tree, Random Forest, Gradient Boosting, XGBoost, LightGBM, AdaBoost, Naive Bayes |
| Class imbalance | Balanced sample weights during training |
| Validation | 5 repeated 80/20 holdouts grouped by PSU (`v021`), so no cluster appears in both train and test |
| Survey weights | Test metrics computed with the DHS weight (`v005 / 1,000,000`), alongside unweighted metrics |
| Explainability | SHAP (mean absolute values) and weighted permutation importance (drop in weighted AUC) |

**Note on the maternal PNC outcome:** it is built only from the maternal postnatal-check variables `m62` and `m66`. The newborn check `m70` is deliberately **not** used. [`maternal_pnc_reconstruction.py`](maternal_pnc_reconstruction.py) builds and audits this outcome.

## Repository contents

| File | Purpose |
| --- | --- |
| `main_analysis.ipynb` | Full analysis notebook (outcome audit, model comparison, SHAP, figures) |
| `model_comparison.py` | Eight-model comparison with PSU-grouped holdouts and weighted metrics |
| `maternal_pnc_reconstruction.py` | Builds and audits the maternal PNC outcome |
| `feature_importance_shap.py` | SHAP and weighted permutation importance for the selected models |
| `model_comparison_summary.csv` | Summary model-comparison results |
| `shap_importance_summary.csv` | Summary SHAP importance |
| `weighted_permutation_importance.csv` | Weighted permutation importance |
| `requirements.txt` | Python dependencies |

## Reproducing the analysis

1. Request access to the 2024 NDHS from [The DHS Program](https://dhsprogram.com/) and download the Children's Recode (KR) Stata file.
2. Install the dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Set the path to your local `.dta` file in the notebook or scripts. They currently point to a Google Colab path (`/content/...`).
4. Run `main_analysis.ipynb`, or run the scripts in this order: `maternal_pnc_reconstruction.py` → `model_comparison.py` → `feature_importance_shap.py`.

DHS microdata are **not** included in this repository and must not be shared, under DHS data-use terms.

## Citation

Please cite the archived release on Zenodo, which gives the full author list and citation formats: https://doi.org/10.5281/zenodo.24247128

## License

Code is released under the Apache License 2.0. Third-party materials and restricted DHS microdata are not covered by this license.

## Author

**Abdullahi Abdisalam Mohamed** · Data Analyst, public-health data science
GitHub: [@abdallla-99](https://github.com/abdallla-99) · Email: abdallagarweyne9@gmail.com
