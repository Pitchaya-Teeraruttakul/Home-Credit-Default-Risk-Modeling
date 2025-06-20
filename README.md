# Home-Credit-Default-Risk-Modeling

# Project Overview 👀

This repository provides an end-to-end implementation of a Probability of Default (PD) modeling framework aligned with IFRS 9 requirements, Home Credit Default Risk dataset. It covers data preparation, feature engineering, WOE binning and IV analysis, logistic regression PD modeling, segmentation/scorecard design, IFRS 9 segmentation criteria, calibration of PIT and lifetime PD, and ECL computation.

# Objectives 🪵

    - Build a robust logistic regression PD model using WOE-transformed features
    - Calibrate Point-in-Time (PIT) and Lifetime PD, incorporating macroeconomic adjustments
    - Apply IFRS 9 segmentation criteria to derive staging buckets
    - Compute Expected Credit Loss (ECL) under IFRS 9 frameworks (12‑month and Lifetime PD)
    - Showcase full credit risk modeling workflow for portfolio-level risk assessment

# Data Preparation 🧹

    - application_train.csv: Main table with borrower-level features.
    - bureau.csv: All client's previous credits provided by other financial institutions that were reported to Credit Bureau
    - bureau_balance.csv: Monthly balances of previous credits in Credit Bureau.
    - previous_application.csv: All previous applications for Home Credit loans of clients who have loans in our sample.

    In the dataExploration.ipynb, I perform a detailed exploratory data analysis (EDA) and cleaning step based on the raw CSV files.
    Key analyses and actions include:
        - Missing Value Analysis
        - Univariate Distribution Checks
        - Categorical Value Cleaning
        - Basic Flag Features
        - Merging External Tables
    and etc.
    After cleaning and merging, the consolidated DataFrame contains 307,511 rows and approximately 98 features.

# Feature Engineering ❓

    In the featureSelectionAndEDA.ipynb, we expand upon EDA to create and select predictive features:
        - Correlation & Multicollinearity Checks
        - Ratio & Scale Features
        - Employment & Demographic Features
        - Aggregation

    WOE Binning & IV Analysis --> Binning Strategy and encoding all the essential features, ready for modeling
    also Compute Information Value (IV) to select predictive variables.

# Modeling 🐌

    In model.ipynb, we implement, train, and evaluate a logistic regression PD model on WOE-transformed features:
        perform logistic regression --> to find raw PD


    Evaluate model using AUC, KS statistic, calibration plots, and confusion matrix.

# Post Modeling 🐌

    PIT PDs Calibration (calibrate Raw PDs to Point-In-Time PDs)
        Convert logistic regression coefficients into a points-based scorecard.
        Calibrate PIT PD to align with empirical 12‑month default rates.


    Scorecard & Segmentation (IFRS 9 Segment to Portfolio Level and Risk Gragd Level)
        Define segmentation buckets based on score bands.


    TTC PDs and Project Lifetime PDs Calibration (calibrate PIT PDs to IFRS9 PDs)
        To identify and segment records to IFRS9 Stage

    Assign Stage 1 (12‑month PD), Stage 2 (significant increase in credit risk), Stage 3 (defaulted) per IFRS 9 rules.

    ECL Computation (Uses result IFRS9 PDs to calculate Expeced-Credit-Loss)

        Combine EAD (Exposure at Default), LGD (Loss Given Default), and PD (12‑month or Lifetime) to compute ECL:
        EL = PD * EAD * LGD

        ECL = sum(EL) along Portfolio
        to be credit cost of Bank

# References

- IFRS 9 Standard Documentation
  -(Kaggle) Home Credit Default Risk Competition
  -Detail framework : https://www.linkedin.com/posts/alexey-gavrilyuk-8a60a685_milestones-of-pd-calculation-for-ecl-assessment-activity-7316346316001824768-H3nS/?utm_source=chatgpt.com

# License

    This project is licensed under the MIT License. Feel free to adapt and extend.

- the small unique exposture < 30 records make global quantile bins and assign to relative bucket because i dont want to drop any row of post model data!!

then do back test on a hold-out year or out-of-time sample
Document your segmentation rules, thresholds, and calibration approach for audit
then integrate EDA LGD ECL
