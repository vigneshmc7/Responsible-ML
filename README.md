# Responsible-ML
MSBA Responsible Machine Learning Course Assignments

# DNSC 6330 Assignments

This repository contains my Python implementations of the COMPAS analysis assignments completed for DNSC 6330.

Assignment 1 recreates the original machine learning workflow used in class, including data cleaning, exploratory analysis, logistic regression, and model diagnostics. Assignment 2 builds on that work by applying model explanation methods such as SHAP, LIME, and DiCE to better understand the behavior of the COMPAS replacement model. Assignment 3 extends the analysis into a formal disparate impact and fairness audit. Assignment 4 evaluates model reliability under drift, generalization failure, spurious correlations, robustness stress, and subgroup variation. Assignment 5 applies adversarial machine learning concepts to audit evasion, poisoning, privacy leakage, and security governance risks.

## Repository Files

* `Assignment_1.ipynb`
* `Assignment_2.ipynb`
* `Assignment_3.ipynb`
* `Assignment_4.ipynb`
* `Assignment_5.ipynb`
* `Assignment_5_Report.txt`
* `compas-scores-two-years.csv`

The dataset file should be kept in the same folder as the notebooks for assignments that load the dataset locally. Later notebooks may also download the ProPublica COMPAS dataset directly from its public GitHub source so they can run in Google Colab without a local data file.

---

## Assignment 1

`Assignment_1.ipynb` recreates the in-class workflow in Python. The notebook follows the same sequence as the original R analysis and includes:

* loading the COMPAS dataset
* filtering and cleaning the data
* creating and transforming variables
* exploratory data analysis and descriptive statistics
* visualizations of age, race, score category, and prior offenses
* logistic regression model development
* confusion matrix, accuracy, precision, recall, false positive rate, and false negative rate
* subgroup analysis by race

The purpose of this notebook is to reproduce the same analytical conclusions from the original workflow while using Python libraries.

### Libraries Used

* pandas
* numpy
* matplotlib
* seaborn
* scikit-learn
* statsmodels

---

## Assignment 2

`Assignment_2.ipynb` continues directly from Assignment 1 and focuses on explaining the COMPAS replacement model.

The notebook includes:

* SHAP beeswarm summary plot for the full test set
* SHAP waterfall plots for the highest-risk and lowest-risk Black and White defendants
* LIME explanations for the same four individuals
* comparison of SHAP and LIME feature attributions
* DiCE counterfactual examples showing the smallest changes needed to change the model prediction
* identification of whether any counterfactual depends on immutable features such as race or sex
* a short governance memo discussing the strengths and limitations of the model

The explanation methods generally identify age, priors_count, decile_score, and days_b_screening_arrest as the main factors influencing predictions. The notebook also discusses why explanation methods can differ and why fairness should not be evaluated using only one explanation technique.

### Additional Libraries Used

* shap
* lime
* dice-ml

---

## Assignment 3

`Assignment_3.ipynb` extends the COMPAS analysis by performing a formal disparate impact and fairness audit of the prediction model developed in Assignment 1.

The notebook includes:

* calculation of Adverse Impact Ratio (AIR), Marginal Effect (ME), and Standardized Mean Difference (SMD) separately for race and sex
* comparison of manual fairness calculations with the solas-ai library to confirm identical results
* evaluation of disparate impact using the EEOC 80% rule
* intersectional fairness analysis for combined race and sex groups
* identification of the worst-affected subgroup based on AIR
* calculation of false positive rate (FPR) and false negative rate (FNR) disparities by race
* statistical testing of FPR and FNR differences using two-proportion z-tests
* publication-quality grouped bar chart comparing FPR and FNR across racial groups with Caucasian defendants as the reference group
* a compliance memo written for a hypothetical regulator summarizing findings, fairness metrics, and limitations

The analysis found evidence of disparate impact by race. African-American defendants had an AIR below the 0.80 threshold relative to Caucasian defendants, and African-American males were the most adversely affected subgroup in the intersectional analysis. The model also showed a substantially higher false positive rate for African-American defendants and a higher false negative rate for Caucasian defendants, illustrating the tradeoff between calibration and error-rate parity.

### Additional Libraries Used

* solas-ai
* scipy

---

## Assignment 4

`Assignment_4.ipynb` evaluates whether the COMPAS model remains reliable beyond standard train-test performance. The notebook focuses on distribution drift, generalization, spurious-correlation risk, robustness under stress, and subgroup fairness.

The notebook includes:

* loading and preprocessing the COMPAS dataset using the same inclusion criteria as the model development workflow
* training a logistic regression baseline and a gradient-boosted tree challenger model
* calculation of Population Stability Index (PSI) and Kolmogorov-Smirnov (KS) statistics for numeric features
* calculation of Maximum Mean Discrepancy (MMD) in the encoded feature space
* comparison of train and test score distributions for both models
* train-test comparison of AUC, accuracy, and log loss to diagnose overfitting
* counterfactual feature swaps for race, gender, and charge type to probe spurious-correlation risk
* stress testing of priors_count and juvenile count-based features
* ICE curves and sensitivity summaries for priors_count
* slice-based model evaluation by race, gender, and age
* a final audit summary connecting each metric to deployment and monitoring implications

The analysis found low train-test distribution drift in the random split, but it also identified important model risk concerns. The gradient-boosted tree showed more overfitting than logistic regression, race swaps produced the largest prediction shifts, and subgroup evaluation showed meaningful false positive and false negative rate differences by race and age. The notebook concludes that aggregate performance is not sufficient for deployment review and that subgroup monitoring, out-of-time validation, and sensitivity documentation are necessary.

### Additional Libraries Used

* scipy
* scikit-learn

---

## Assignment 5

`Assignment_5.ipynb` applies adversarial machine learning and security audit methods to the COMPAS recidivism model. The notebook evaluates deployment-time evasion, training-time poisoning, feature-level drift monitoring, membership inference risk, and mitigation tradeoffs.

The notebook includes:

* training logistic regression and gradient-boosted tree models on the COMPAS recidivism target
* clean-model baseline reporting for AUC, false positive rate by race, and Adverse Impact Ratio (AIR)
* PGD-style evasion testing across epsilon values of 0.25, 0.50, 1.00, and 2.00
* comparison of evasion effects on LR and GBT models
* reporting of FPR by race and AIR under each evasion setting
* label-flip poisoning loops targeting both African-American and Caucasian defendants
* AUC and AIR degradation curves for both poisoning variants
* identification of poisoning conditions where AUC remains stable while AIR remains outside the acceptable range
* PSI-based drift monitoring analysis to test whether feature drift would detect label poisoning
* shadow-model membership inference attack for LR and GBT
* confidence-gap histograms for training members and test non-members
* comparison of generalization gap and membership inference AUC
* L2 regularization analysis for logistic regression across C values of 0.01, 0.10, 1.00, and 10.00
* a final reflection identifying the highest-risk finding and proposing proactive and reactive mitigations

The analysis found that PGD-style evasion changes false positive rates and subgroup harm differently across the two model types. The clearest security risk was label-flip poisoning: AUC stayed close to baseline while AIR remained outside the practical fairness range, and feature-level PSI did not detect the attack because the feature distributions were unchanged. Membership inference AUC was close to random guessing in this experiment, and L2 regularization did not materially change privacy leakage or fix AIR disparity. The written report in `Assignment_5_Report.txt` summarizes these findings and recommends data provenance controls, label audit sampling, and subgroup fairness monitoring.

### Additional Libraries Used

* scipy
* scikit-learn
