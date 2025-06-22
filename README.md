# Home-Credit-Default-Risk-Modeling 🏡

# Project Overview 👀

This repository provides an end-to-end implementation of a Probability of Default (PD) modeling framework aligned with IFRS 9 requirements, Home Credit Default Risk dataset. It covers data preparation, feature engineering, WOE binning and IV analysis, logistic regression PD modeling, segmentation/scorecard, IFRS 9 segmentation criteria, calibration of PIT and lifetime PDs, and fortfolio ECL computation.

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

    In dataExploration.ipynb, I perform a detailed exploratory data analysis (EDA) and cleaning step based
        on the raw CSV files.
    Key analyses and actions include:
        - Missing Value Analysis
        - Univariate Distribution Checks
        - Categorical Value Cleaning
        - Basic Flag Features
        - Merging External Tables
        and etc.
    After cleaning and merging, the consolidated DataFrame contains 307,511 rows and approximately 98 features.

# Feature Engineering ❓

    In featureSelectionAndEDA.ipynb, we expand upon EDA to create and select predictive features:
        - Correlation & Multicollinearity Checks
        - Ratio & Scale Features
        - Employment & Demographic Features
        - Aggregation

    WOE Binning & IV Analysis --> Binning Strategy and encoding all the essential features, ready for modeling
    also Compute Information Value (IV) to select predictive variables.

# Modeling 🐌

    In model.ipynb, we implement, train, and evaluate a logistic regression PD model on WOE-transformed features:
        Perform logistic regression --> to find raw PD
        Do randomSearch (for the most time efficiency) --> Hyperparameter Tuning
        Evaluate model using AUC, KS statistic, calibration plots.

# Post Modeling 🐌

    1. PIT PDs Calibration (calibrate Raw PDs from logistic regression model prediction to Point-In-Time PDs)
        Calibrate PIT PD to align with empirical 12‑month default rates using #Isotonic Regression
        Why isotonic over simpler method
            the regression clip out-of-range inputs, guarantee every raw PD or extreme ones gets a valid calibrated PD
            For transpalency --> The fitted mapping is easy to visualize (a step function) and explain to auditors


    2. Scorecard & Segmentation (IFRS 9 Segment to Portfolio Level and Risk Gragd Level)
        - Define segmentation buckets based on possible features (Portfolio Level) --> features that share similar credit
        risk characteristics Ex. 'FLAG_OWN_REALTY' which contain (0:Unsecured,1:Secured), and etc.
        If Credit scoring is require --> specify criterias and score length to do credit scoring by IFRS9 (thrshold Bucketing
        specify the thrshold cutoff for each risk level --> Ex. 0-1%, 1-2%, ..., >10%.)


    3. TTC PDs and Project Lifetime PDs Calibration (calibrate PIT PDs to IFRS9 PDs)
        - In this step gonna a little bit tricky 🥲 (the data set not support this process requirement data --> use proxy
        and simulate data instead)

        - In this process need to project 1-year PIT PDs to Lifetime PDs but data not support format for the time series data
        or customer credit tracking along the time which i need to refer the rest lifetime length of the loan by using
        empirical data in another tabel which weren't feated in the logistic regression model (previous_application.csv) -->
        also had data preprocessing (checking missing value + proper impusing adjust by original data distribution) again
        along the data set before merged into the calibration table

        - Then do calibrate PIT PDs to TTC PDs for smoothing those macro economic fructuation --> find targetPD by IFRS9 TTC
        minimum brenchmark for # proxy TTC --> lifetime (as I mention above this data don't contain any of time series to
        calculate long-run PDs --> use proxy benchmark instead --> for more detail benchmark numbers in the coding files)

        - Estimate Roughly EAD and LGD of the loan by each records substitude data and records characteristic.

        - Fetch Macro economic features for lifetime projection (time length of fred data --> ceiling of max remaining loan
        lifetime --> 2018 - 2022)

        - Projected lifetime PDs as time series of the remaining lifetime (years)

    4. ECL Computation (Uses result IFRS9 PDs to calculate Expeced-Credit-Loss)

        Before Ecl calculation after get rough proxy EAD-base Lifetime PDs, I had classified dataset to IFRS9 stages (3
        stages). which have criterias as follow:
            stage 3 TARGET = 1 (already default) | DPD-120 != 0 --> use Lifetime PDs as IFRS9 and portfolio PDs in ECL
                    calculation
            stage 2 PIT PD above 0.2 --> use Lifetime PDs as IFRS9 and portfolio PDs in ECL calculation
            stage 1 the rest of the records which not in rule above --> use PIT PD (1 year point-in-time) PDs as IFRS9 and
                    portfolio PDs in ECL calculation


        Combine EAD (Exposure at Default), LGD (Loss Given Default), and IFRS9 PDs by stage (12‑month or Lifetime PDs) to
        compute portfolio ECL as formula:
        EL(Exspected Loss) = PD (Probability of default) * EAD (Exposeture at Default)* LGD (Loss Given Default) by stages

        ECL = forsum(EL) along Portfolio
        to be credit cost of Bank

        Finally, I got portfolio ECL = 5,568,675,622.186 USD

# Backtesting? --> stress testing or forward looking predictions? 🧪

# Dataset and Tools Specification 🔨

    1. Data set
        Home Credit Default Risk Modeling (Kaggle Case Competition 2018) --> # Real-World Credit Risk Data
    Files --> can't upload via github cause of every file too large
        application_test.csv ❌ # don't have TARGET feature --> for accuracy evaluation in case competition
        application_train.csv ✅
        bureau.csv ✅
        bureau_balance.csv ✅
        credit_card_balance.csv ❌
        HomeCredit_columns_description.csv ✅
        installments_payments.csv ✅
        POS_CASH_balance.csv ❌
        previous_application.csv ✅
        sample_submission.csv ❌

    2. Programming Language
        Python 3.12.2 with libraries
        - Pandas
        - Numpy
        - IPython
        - Sklearn
        - Matplotlib
        - Seaborn
        - joblib
        - etc.

    3. IDE --> VScode
    4. Version Control --> github

# References 🪞

    - IFRS 9 Standard Documentation
    -(Kaggle) Home Credit Default Risk Competition
    -Detail framework : https://www.linkedin.com/posts/alexey-gavrilyuk-8a60a685_milestones-of-pd-calculation-for-ecl-
    assessment-activity-7316346316001824768-H3nS/?utm_source=chatgpt.com

# License 📃

    This project is licensed under the MIT License. Feel free to adapt and extend.
