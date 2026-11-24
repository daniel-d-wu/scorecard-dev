# End-to-End Scorecard Development — LendingClub Public Data

## Disclaimer

This repository is for demonstrative purposes only. It uses public LendingClub loan data that is widely used in Kaggle projects ([Kaggle: Lending Club Loan Data](https://www.kaggle.com/datasets/wordsforthewise/lending-club)). It does not use, reproduce or represent any proprietary models, data or documentation. The approach taken is a generic scorecard method. Internal models typically involve more intensive treatment over the pre-model fitting stages of scorecard development, and sometimes involves vendors to assist in steps laid out in the proposed roadmap (e.g. credit bureaus to identify surrogates in reject inferencing, or vendors for algorithmic binning). Proprietary data is also more granular.  

## Overview

Development of an application probability-of-default (PD) scorecard for unsecured personal loans, structured to follow model risk management standards: conceptual soundness, modeling framework, variable selection, performance assessment, implementation and ongoing monitoring. All work is in `scorecard_dev.ipynb`.

## Roadmap

| # | Stage | Step | Detail | Status |
|---|---|---|---|---|
| 1 | Conceptual soundness | Business purpose and scope | Application PD scorecard for individual, new-borrower unsecured personal loans; champion = LendingClub `sub_grade` | Done |
| 2 | | Population | Pseudo-policy exclusions (joint, out-of-policy, FICO < 660, < 36 months history, > 6 inquiries, currently delinquent): 2,128,546 loans | Done |
| 3 | | Data review and leakage screen | 151 fields reduced to 76 candidates; post-origination, post-decision, out-of-scope and compliance fields removed | Done |
| 4 | Modeling framework | Target definition | Lifetime outcome, 36-month loans. Bad = charged off / default / 31–120 days late; Good = fully paid; 16–30 days late and grace period indeterminate | Done |
| 5 | | Sample design | Development Jan-2013 – Jun-2015 (70/30 train/holdout, stratified by half-year × bad); out-of-time Jul–Dec 2015; 543,131 loans | Done |
| 6 | | Business segmentation | Single segment; FICO tier, loan purpose and file depth carried to empirical testing | Done |
| 7 | | Reject inference | Known good/bad model, fuzzy augmentation, reject risk adjustment and booked/inferred mix sensitivity; diagnostic only, final model on accepted loans | Done |
| 8 | | Empirical segmentation | Tree-proposed splits tested as segment-level vs single models; best lift ≈ +0.2 Gini, below the +1.0 threshold; single model retained | Done |
| 9 | Variable selection | Feature engineering | 19 derived attributes (affordability, request vs existing debt, utilization, account mix, recency, recodes); 94 candidates after duplicate removal | Done |
| 10 | | Pre-filters | Availability, single-value, compliance, superseded-recode and leakage filters on raw and derived attributes: 94 → 72 candidates | Done |
| 11 | | Fine binning and WoE / IV screen | Optimal binning (`optbinning`), missing values in a separate bin; 72 → 14 candidates pass IV ≥ 0.05, all monotonic and stable on holdout and out-of-time | Done |
| 12 | | Model estimation | Forward stepwise logistic regression on WoE, entry by pseudo-R² gain ≥ 0.001 with coefficient-sign checks: 9 attributes selected; Gini 33.4 holdout / 35.0 out-of-time vs `sub_grade` 32.1 / 36.3 | Done |
| 13 | | Coarse binning | Deferred: selected attributes keep their fine bins for now; plan to merge to about 5–8 bins per attribute and refit | Deferred |
| 14 | | Collinearity | All 9 VIFs below 1.7 and stable on holdout and out-of-time; all coefficients negative; two moderately correlated pairs retained after a drop-one test | Done |
| 15 | Performance assessment | Scaling and calibration | 600 points at 50:1 good:bad odds, 20 points to double the odds; calibrated on holdout (actual/predicted 1.00); out-of-time under-predicted by 8%, a level shift to revisit in monitoring | Done |
| 16 | | Discrimination | KS, Gini / AUC and rank-ordering vs `sub_grade` | WIP |
| 17 | | Attribute summary | Final attributes, bins and points | WIP |
| 18 | | Holdout and out-of-time validation | Performance stability and attribute PSI | WIP |
| 19 | | Fair lending | Disparate impact framework (no protected-class data available; described only) | WIP |
| 20 | | Adverse action reason codes | Top reasons by points lost vs best bin | WIP |
| 21 | | Cutoff strategy | Approval rate vs bad rate trade-off; swap-set analysis vs `sub_grade` | WIP |
| 22 | Implementation | Implementation and governance | Score code validation and approval process (described only) | WIP |
| 23 | Ongoing monitoring | Stability and performance monitoring | PSI, CSI and performance tracking on 2016–2017 vintages as post-launch data | WIP |
| 24 | | Recalibration triggers | Threshold-based redevelopment / recalibration criteria | WIP |

## Data

Download the dataset from the Kaggle link above and place these two files in the same folder as the notebook. They are excluded from the repository due to size.

- `accepted_2007_to_2018Q4.csv` (1.6 GB)
- `rejected_2007_to_2018Q4.csv` (1.7 GB)

## Environment Setup

Tested on Windows 11 with Python 3.12. Pinned versions are in `requirements.txt`.

**1. Create an isolated environment** (Anaconda / Miniconda):

```
conda create -n scorecard python=3.12 pip -y
conda activate scorecard
pip install -r requirements.txt
```

**2. Register it as a Jupyter kernel:**

```
python -m ipykernel install --user --name scorecard --display-name "Python 3.12 (scorecard)"
```

**3. Run the notebook:** open `scorecard_dev.ipynb` in JupyterLab from the folder that holds the CSV files, select the **Python 3.12 (scorecard)** kernel, and run all cells. A full run takes about four minutes and needs roughly 12 GB of free memory, since the accepted-loans file is about 6.3 GB once loaded.

**Notes**

- Use a dedicated environment. In a full Anaconda base environment on Windows, `optbinning`'s dependency `ortools` and Anaconda's `pyarrow` ship conflicting DLLs, and one of them fails to import.
- The setup cell disables pandas' `numexpr` acceleration (`pd.set_option('compute.use_numexpr', False)`). Multi-threaded `numexpr` returned intermittent incorrect results during development. The pinned environment does not install `numexpr`.
- The notebook reads the CSV files by relative path, so the kernel's working directory must be the notebook folder (the default when the notebook is opened in JupyterLab).
