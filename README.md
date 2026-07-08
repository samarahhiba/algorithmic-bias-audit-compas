# Algorithmic Bias Audit — COMPAS Recidivism Risk Model

A research-style fairness audit of a recidivism-risk classifier using the ProPublica COMPAS two-year recidivism dataset. This project studies how a model can appear reasonably accurate overall while producing unequal subgroup error rates, and compares mitigation strategies that trade off predictive performance, interpretability, and fairness.

**Research focus:** algorithmic bias, subgroup error analysis, fairness metrics, and mitigation in a high-stakes criminal-justice setting.  
**Presented at:** San Francisco Bay University, Summer 2026, as part of an AI and machine-learning research project presentation.

---

## Project Overview

The goal of this project is not only to train a competent classifier, but to audit whether the classifier distributes errors unevenly across racial subgroups. The baseline model excludes race from the input features, then uses race only for fairness auditing and mitigation analysis.

The project asks:

> Can a model look acceptable by overall accuracy while still producing unequal error burdens across racial groups?

The answer from this audit is yes. The baseline logistic-regression model achieved reasonable overall performance, but subgroup confusion matrices showed meaningful differences in high-risk prediction rates, true positive rates, and false positive rates.

---

## Key Findings

| Method | Accuracy | Recall | F1 | High-Risk Ratio | Equalized-Odds Gap |
|---|---:|---:|---:|---:|---:|
| Baseline Logistic Regression | 0.666 | 0.562 | 0.613 | 1.959 | 0.290 |
| Reweighted Logistic Regression | 0.659 | 0.648 | 0.641 | 1.824 | 0.292 |
| Group-Specific Thresholds | 0.655 | 0.703 | 0.657 | 1.070 | 0.040 |

The strongest technical mitigation was group-specific threshold adjustment. It reduced the equalized-odds gap from **0.290** to **0.040**, while only slightly lowering accuracy from **0.666** to **0.655**.

The ethical trade-off is important: group-specific thresholds improve fairness by balancing error rates, but they require explicit use of group membership at the postprocessing stage. The final recommendation is that this type of model should not be used as an automated decision-maker. If used at all, it should be limited to decision support with human review, subgroup monitoring, transparent documentation, and recurring re-audits.

---

## Methods

### Baseline Model

The baseline is a logistic-regression classification pipeline. Logistic regression was selected because the project prioritizes interpretability and fairness auditing over maximizing raw predictive power.

The model uses features such as:

- age
- prior count
- juvenile offense counts
- charge degree
- sex

Race is excluded from the baseline model features. However, excluding race does not guarantee fairness because other variables can still act as proxies for historical or structural bias.

### Fairness Audit

The audit computes multiple fairness metrics, including:

- demographic parity gap
- true positive rate gap / equal opportunity gap
- false positive rate gap
- equalized-odds gap
- calibration within groups
- high-risk prediction ratio / disparate impact-style comparison
- subgroup confusion matrices

### Mitigation Strategies

Two mitigation strategies are compared:

1. **Reweighting**
   - A training-time mitigation method.
   - Gives more influence to underrepresented group-label combinations during model training.
   - Improves recall and slightly reduces high-risk imbalance, but does not meaningfully reduce the equalized-odds gap.

2. **Group-Specific Thresholds**
   - A postprocessing mitigation method.
   - Applies different probability cutoffs for the audited groups.
   - Produces the strongest fairness improvement in this experiment, reducing the equalized-odds gap to 0.040.

---

## Data

This repository does **not** include the raw COMPAS dataset. The notebook loads the data directly from ProPublica if `compas-scores-two-years.csv` is not found locally.

Expected local filename, if downloaded manually:

```text
compas-scores-two-years.csv
```

Public source:

```text
https://raw.githubusercontent.com/propublica/compas-analysis/master/compas-scores-two-years.csv
```

Original repository:

```text
https://github.com/propublica/compas-analysis
```

---

## Repository Structure

```text
algorithmic-bias-audit-compas/
├── README.md
├── requirements.txt
├── data/
│   └── README.md
├── notebooks/
│   └── algorithmic_bias_audit_compas.ipynb
├── report/
│   ├── Project7_Algorithmic_Bias_Audit_Report.docx
│   └── Project7_Algorithmic_Bias_Audit_Report.pdf
└── slides/
    └── Project7_Algorithmic_Bias_Audit_Slides.pptx
```

---

## How to Run

1. Clone the repository:

```bash
git clone https://github.com/samarahhiba/algorithmic-bias-audit-compas.git
cd algorithmic-bias-audit-compas
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Open the notebook:

```bash
jupyter notebook notebooks/algorithmic_bias_audit_compas.ipynb
```

The notebook will either use a local copy of `compas-scores-two-years.csv` or load the public ProPublica CSV directly.

---

## Research Contribution

This project demonstrates that overall model accuracy is not enough for evaluating high-stakes machine-learning systems. The audit shows how subgroup confusion matrices can reveal unequal error burdens that are hidden by aggregate metrics.

The project also shows that fairness mitigation is not one-size-fits-all. Reweighting and threshold adjustment affect different fairness metrics in different ways. The final decision depends on which harm is most important to reduce and what trade-off is ethically defensible.

---

## Limitations

This project is a research-style fairness audit, not a deployment-ready criminal-justice tool. Limitations include:

- The analysis depends on the historical labels and assumptions in the COMPAS dataset.
- Fairness metrics can conflict, especially when subgroup base rates differ.
- Group-specific thresholds may raise legal and ethical concerns.
- The model should not be used for automated decision-making.

---

## Final Recommendation

The model should **not** be used as an automated decision-maker. If used at all, it should only support human decision-making and must include:

- human review
- transparent documentation
- subgroup performance monitoring
- recurring fairness audits
- clear justification of the chosen operating threshold

The central lesson is that fairness is not a single score. It is a defended operating choice that must state which harm is reduced and what trade-off is accepted.

---

## References

- ProPublica COMPAS Analysis Repository
- ProPublica COMPAS Two-Year Recidivism Dataset
- Hardt, Price, and Srebro (2016), *Equality of Opportunity in Supervised Learning*
- Kleinberg, Mullainathan, and Raghavan (2016), *Inherent Trade-Offs in the Fair Determination of Risk Scores*
