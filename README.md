# FraudScope — Fraud Detection Research Project

FraudScope is a reproducible, Colab-friendly fraud detection project. It starts with an IEEE-CIS supervised baseline, then adds careful validation, transaction-behavior features, cost-aware evaluation, anomaly scoring, and an investigation-oriented reporting layer.

## Important data note
The real IEEE-CIS competition data is **not included** in this ZIP. It is large and governed by Kaggle competition terms. A small **synthetic sample** is included only to test that the project structure and pipeline run; it is not suitable for meaningful model-performance claims.

Dataset page: https://www.kaggle.com/competitions/ieee-fraud-detection/data

Required files:
- `train_transaction.csv`
- `train_identity.csv`

The tables are joined on `TransactionID`; the target is `isFraud`.

## Fastest path: Google Colab
1. Upload this ZIP to Google Drive or open Colab at https://colab.research.google.com/.
2. Upload and unzip `FraudScope.zip` in Colab, or upload `notebooks/FraudScope_M1.ipynb`.
3. Run notebook cells from top to bottom.
4. Keep `USE_SYNTHETIC_SAMPLE = True` for a quick smoke test.
5. To use IEEE-CIS, download the two required CSVs from Kaggle and place them in `data/raw/ieee_cis/` (or change `DATA_DIR` in the notebook). Set `USE_SYNTHETIC_SAMPLE = False`.
6. The real dataset can be large. Use a Colab runtime with enough RAM and avoid printing entire dataframes.

## Local setup
Python 3.10+ recommended.

```bash
pip install -r requirements.txt
python -m pip install -e .
pytest -q
```

The first milestone notebook is the guided entry point:
`notebooks/FraudScope_M1.ipynb`

## Project workflow
1. Data/schema audit
2. Join transaction and identity tables
3. Leakage-aware chronological train/validation/test split using `TransactionDT`
4. Train-only preprocessing
5. LightGBM baseline
6. Evaluation with average precision, precision, recall, F1 and recall at a fixed alert budget
7. Feature engineering ablations
8. Optional anomaly score experiment
9. Future extensions: calibrated cost-sensitive decisions, explainability, drift monitoring and graph features if the dataset supports defensible entity links.

## Design principles
- Never fit imputers or encoders on validation/test data.
- Never use `isFraud` as a feature.
- Exclude `TransactionID` from model inputs by default.
- Use `TransactionDT` for chronological ordering; treat it as an elapsed-time offset, not a wall-clock timestamp.
- Do not claim that shared identifiers prove fraud rings.
- Do not use the included synthetic data to report benchmark results.
- Preserve a clean separation between exploratory notebooks, reusable source code, tests and reports.

## Folder map
- `notebooks/` — Colab walkthrough
- `src/fraudscope/` — reusable audit and evaluation helpers
- `tests/` — quick tests
- `data/sample/` — synthetic smoke-test data only
- `docs/` — workflow and research map
- `reports/` — generated audit reports and experiment outputs

## Current milestone acceptance criteria
- Notebook runs end-to-end on the synthetic sample.
- Schema and missingness report is generated.
- Chronological split is verified.
- A baseline model produces metrics without test-set tuning.
- Real-data results are reported separately from smoke-test results.
