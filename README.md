# Application Scorecard Development — LendingClub Public Data

## Disclaimer

This project is for **demonstrative purposes only**. It uses **public data**: the LendingClub loan dataset widely used in Kaggle projects ([Kaggle: Lending Club Loan Data](https://www.kaggle.com/datasets/wordsforthewise/lending-club)). It does not use, reproduce or represent any lender's proprietary models, data or documentation.

## What this is

A step-by-step build of an **application PD scorecard** for unsecured personal loans, following the sequence a model-risk / validation team expects: scope → population → data & leakage screen → target → sample design → segmentation → reject inference → feature engineering → binning / WoE → model → validation → monitoring.

The notebook `scorecard_dev.ipynb` opens with a 24-step roadmap and status. Steps 1–9 are complete:

| Step | Summary |
|---|---|
| 1–2 | Scope (approve/decline PD model; champion = LendingClub `sub_grade`); pseudo-policy population of 2.13M loans |
| 3 | Field inventory and leakage screen: 151 fields → 76 candidates |
| 4 | Target: lifetime outcome on 36-month loans (Bad = charged off / default / 31–120 days late) |
| 5 | Samples: Jan-2013 – Jun-2015 development (70/30 stratified train/holdout); Jul–Dec 2015 out-of-time |
| 6 | Business segmentation review → single business segment |
| 7 | Reject inference demonstration: KGB model, fuzzy augmentation, sensitivity to the hidden-risk assumption |
| 8 | Empirical segmentation lift test (segment vs single models) → single model |
| 9 | 19 derived attributes (affordability, request vs debt, utilization, account mix, recency, recodes) |

## Data

Download from the Kaggle link above and place these files next to the notebook (they are git-ignored):

- `accepted_2007_to_2018Q4.csv`
- `rejected_2007_to_2018Q4.csv`

## Environment

Python 3 with `pandas`, `numpy`, `scikit-learn` and `statsmodels`. Later steps add `optbinning`.
