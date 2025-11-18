# DILI Risk Prediction using Interpretable Machine Learning

This project builds an **early-screening ML model** to predict **Drug-Induced Liver Injury (DILI)**  
from simple, interpretable molecular descriptors. Although the dataset is **synthetic**, it is
designed to mimic realistic structure–toxicity patterns observed in drug discovery.

---

## 1. Problem Statement

Drug-induced liver injury is a leading cause of:

- Late-stage clinical failures  
- Market withdrawals  
- Serious safety concerns in patients  

Traditional toxicity assessment relies on **animal studies and clinical trials**, which are:

- Slow (months–years)  
- Extremely expensive  
- Ethically challenging  
- Not scalable to thousands of candidate molecules  

The goal is to build a **computational early-warning model** that can flag high-risk compounds
based only on their molecular properties, so that chemists can prioritise safer candidates earlier.

---

## 2. Data

The dataset contains **2,000 synthetic compounds** with:

- `LogP`
- `Molecular_Weight`
- `H_Bond_Donors`
- `H_Bond_Acceptors`
- `Rotatable_Bonds`
- `Aromatic_Rings`
- `CYP3A4_Substrate` (0/1)
- `DILI_Risk` (target: 0 = Safe, 1 = Toxic)

Key observations:

- Class distribution: **≈61% Safe**, **≈39% Toxic** (slightly imbalanced).  
- Toxic compounds tend to have:
  - higher **LogP**
  - higher **Molecular_Weight**
  - more **Rotatable_Bonds**
  - more often **CYP3A4 substrates**

EDA includes:

- Target distribution plots (counts + pie chart)  
- Feature histograms (overall + split by DILI_Risk)  
- Correlation matrix & feature–target correlations  
- T-tests comparing Safe vs Toxic for each descriptor  

---

## 3. Modelling Approach

### Models evaluated

Four models were compared on a held-out test set:

- Logistic Regression  
- Random Forest  
- Gradient Boosting  
- XGBoost  

Metrics: **Accuracy, Precision, Recall, F1, ROC-AUC**.

### Model comparison (test set)

| Model                | Accuracy | Precision | Recall | F1    | ROC-AUC |
|----------------------|----------|-----------|--------|-------|---------|
| Logistic Regression  | 0.8200   | 0.7471    | 0.8141 | 0.7791| 0.8930  |
| XGBoost              | 0.7975   | 0.7551    | 0.7115 | 0.7327| 0.8813  |
| Gradient Boosting    | 0.7925   | 0.7450    | 0.7115 | 0.7279| 0.8811  |
| Random Forest        | 0.7950   | 0.7500    | 0.7115 | 0.7303| 0.8699  |

**Final choice:**  
📌 **Logistic Regression** was selected as the final model because:

- It achieved the **highest ROC-AUC (0.893)**  
- It showed strong **Recall and F1** for the Toxic class  
- It is **fully interpretable**, with coefficients that align with domain intuition

---

## 4. Model Evaluation

For the final Logistic Regression model:

- 5-fold cross-validation (training set)  
  - ROC-AUC ≈ **0.90 ± 0.03**  
  - Stable Accuracy, Recall and F1 across folds

- Test set evaluation:
  - Classification report (Safe vs Toxic)  
  - Confusion matrix + heatmap  
  - ROC curve (AUC ≈ 0.89)

Interpretation:

- The model reliably separates **Safe vs Toxic** compounds.  
- Good **recall for Toxic** is important: better to flag a few safe compounds than miss a dangerous one.

---

## 5. Interpretability

To keep the model explainable for scientists:

- **Logistic Regression coefficients** are analysed and visualised.
- Positive coefficients (increase DILI risk):  
  - `LogP`, `Molecular_Weight`, `Rotatable_Bonds`, `Aromatic_Rings`, `CYP3A4_Substrate`
- Slight negative coefficient (reduces predicted risk):  
  - `H_Bond_Donors`

Interpretation: compounds that are **more lipophilic, heavier, more flexible, more aromatic, and CYP3A4 substrates** are predicted as higher DILI risk, while additional H-bond donors slightly mitigate risk.

---

## 6. Predicting New Compounds

The project demonstrates how to:

- Take a new compound’s descriptors (LogP, MW, etc.)  
- Pass them through a **saved pipeline (StandardScaler + Logistic Regression)**  
- Get:
  - A **class prediction** (Safe / Toxic)  
  - A **probability score** = DILI risk estimate

Example: a test compound was predicted as **Toxic** with **70.61%** DILI risk.

---

## 7. Deployment Readiness

A `Pipeline` object (scaler + model) is:

- Trained and evaluated in the notebook  
- Saved to disk using `pickle`  
- Reloaded and used for scoring new compounds

This makes it easy to:

- Wrap the model into an API  
- Integrate into a screening script or a simple web app

---

## 8. Limitations & Future Work

- Dataset is **synthetic**; real-world performance must be validated on true DILI datasets.
- Only simple descriptors are used; future work could incorporate:
  - molecular fingerprints
  - graph-based representations
  - in vitro assay features

Potential extensions:

- Test tuned Random Forest / XGBoost on richer features  
- Add model calibration and threshold optimisation  
- Build a small **Streamlit / Flask app** for interactive compound scoring

---


