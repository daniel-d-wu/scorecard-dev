# Application Scorecard Development — LendingClub Public Data

## Disclaimer

This repository is for demonstrative purposes only. It uses public LendingClub loan data that is widely used in Kaggle projects ([Kaggle: Lending Club Loan Data](https://www.kaggle.com/wendykan/lending-club-loan-data)). It does not use, reproduce or represent any lender's proprietary models, data or documentation.

## Overview

Development of an application probability-of-default (PD) scorecard for unsecured personal loans, structured to follow model risk management documentation standards: conceptual soundness, modeling framework, variable selection, performance assessment, implementation and ongoing monitoring. The notebook `scorecard_dev.ipynb` opens with the full 24-step development roadmap and its status.

## Completed Work (Steps 1–9)

| Step | Description |
|---|---|
| 1 | Business purpose and scope; LendingClub `sub_grade` as champion benchmark |
| 2 | Pseudo-policy population: 2,128,546 loans after exclusions |
| 3 | Data review and leakage screen: 151 fields reduced to 76 candidates |
| 4 | Target definition: lifetime outcome, 36-month loans |
| 5 | Sample design: Jan-2013 – Jun-2015 development (70/30 stratified train/holdout); Jul–Dec 2015 out-of-time |
| 6 | Business segmentation: single segment |
| 7 | Reject inference: known good/bad model, fuzzy augmentation, sensitivity analysis on the reject risk adjustment |
| 8 | Empirical segmentation: lift test of segment-level vs single models; single model retained |
| 9 | Feature engineering: 19 derived attributes |

## Data

Files used (excluded from the repository due to size):

- `accepted_2007_to_2018Q4.csv`
- `rejected_2007_to_2018Q4.csv`

## Requirements

Python 3 with `pandas`, `numpy`, `scikit-learn` and `statsmodels`; `optbinning` for the binning stage.
