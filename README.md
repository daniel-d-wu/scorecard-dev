# End-to-End Scorecard Development — LendingClub Public Data

## Disclaimer

This repository is for demonstrative purposes only. It uses public LendingClub loan data that is widely used in Kaggle projects ([Kaggle: Lending Club Loan Data](https://www.kaggle.com/datasets/wordsforthewise/lending-club)). It does not use, reproduce or represent any proprietary models, data or documentation. The approach taken is a generic scorecard method. Internal models typically involve more intensive treatment over the pre-model fitting stages of scorecard development, and sometimes involves vendors to assist in steps laid out in the roadmap (e.g. bureaus to identify surrogates in reject inferencing, or specialty shops for algorithmic binning). Instead, I use available public tools.  

## Overview

Development of an application probability-of-default (PD) scorecard for unsecured personal loans, structured to follow model risk management documentation standards: conceptual soundness, modeling framework, variable selection, performance assessment, implementation and ongoing monitoring. All work is in `scorecard_dev.ipynb`.

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
| 10 | | Pre-filters | Availability, single-value, leakage and compliance filters on raw and derived attributes | WIP |
| 11 | | Fine binning and WoE / IV screen | Optimal binning with special values in separate bins; IV ≥ 0.05 | WIP |
| 12 | | Model estimation | Stepwise logistic regression on WoE; sign and pattern checks | WIP |
| 13 | | Coarse binning | Final bin structure for selected attributes | WIP |
| 14 | | Collinearity | VIF < 10 | WIP |
| 15 | Performance assessment | Scaling and calibration | Points-to-double-odds scaling; predicted vs actual by decile | WIP |
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

Files used (excluded from the repository due to size):

- `accepted_2007_to_2018Q4.csv`
- `rejected_2007_to_2018Q4.csv`

## Requirements

Python 3 with `pandas`, `numpy`, `scikit-learn` and `statsmodels`; `optbinning` for the binning stage.
