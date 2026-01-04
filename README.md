# Online Retail Engagement Baseline

This project provides a reproducible baseline for predicting whether a customer from the UCI Online Retail dataset will make at least one non-cancelled purchase within 30 days after a percentile-based cutoff date. The entire workflow (data detection, cleaning, feature engineering, modeling, evaluation, and explainability) lives inside `notebooks/01_initial_draft.ipynb` so you can run everything from a single notebook.

## Dataset expectations
Place the original `Online Retail.xlsx` file next to this repository. The notebook directly references `Online Retail.xlsx`, so keep that exact name (or adjust the relevant cell). The loader standardizes column names, parses datetimes, computes cancellation flags, and filters malformed rows.

## Environment setup
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```
Then launch Jupyter and open `notebooks/01_initial_draft.ipynb`:
```bash
jupyter notebook
```

## Methodology
1. **Cutoff definition** – Use the 80th percentile of `InvoiceDate` as the temporal split. Labels look at the following 30 days only.
2. **Leak-safe labeling** – For each customer, assign `1` if they have a non-cancelled invoice in the label window (cutoff, cutoff+30], otherwise `0`.
3. **Feature engineering** – Aggregate customer histories up to the cutoff: recency, frequency, monetary, basket stats, unique products, unit-price stats, interpurchase intervals, cancellation ratio, and primary country.
4. **Modeling** – Split customers into stratified train/test partitions. Build a preprocessing pipeline (impute/scale numeric, impute/one-hot categorical) and fit Logistic Regression and Gradient Boosting models. Evaluate with ROC-AUC, PR-AUC, confusion matrix, best F1 threshold, calibration curve, and Brier score. Persist the best pipeline to `outputs/models/model.joblib`.
5. **Explainability** – Inspect logistic regression coefficients and gradient boosting feature importances. Optional SHAP analysis runs if the dependency is available.

## Repository structure
```
README.md
requirements.txt
.gitignore
notebooks/
  01_initial_draft.ipynb
outputs/
  figures/
  models/
```

## Metrics reported
- ROC-AUC and PR-AUC
- Confusion matrix at threshold 0.5
- Best F1 threshold based on precision-recall curve
- Reliability curve and Brier score

## Next steps
- Add rolling-window or walk-forward validation with multiple cutoffs.
- Explore time-to-next-purchase survival modeling for richer retention estimates.
- Evaluate uplift models to prioritize marketing interventions.
- Quantify customer stability/uncertainty to guide acquisition spending.
- Integrate automated hyperparameter tuning and model monitoring.
