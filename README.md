# Bosch Production Line Performance

Trying to predict which parts will fail quality control. 

## Initial Plan

Initial plan generated with Claude.

**01_eda.ipynb** — This is where you set the narrative. The Bosch dataset has a few unusual properties worth highlighting explicitly: ~0.6% defect rate (severe imbalance), ~970 numerical + ~1940 categorical + ~1157 date features across three separate CSV files, and massive sparsity (~80%+ missing values). Key things to investigate: the station/line hierarchy encoded in feature names (e.g. `L0_S0_F2`), whether missing values are structurally meaningful (a part not passing through a station = NaN), and whether date features reveal time-ordering you need to respect in your split.

**02_preprocessing.ipynb** — The 1000+ features problem is the heart of this project. Your strategy should include dropping near-constant and near-duplicate features, sparsity-based filtering, and possibly target-encoding or frequency-encoding the categorical file. Time-aware train/test splitting is critical here — a random split leaks information. Document every decision clearly since this is where most portfolio reviewers look for engineering judgment.

**03_model_comparison.ipynb** — LightGBM is the natural winner for this dataset (tree methods handle sparse data well), but the comparison is the point. Use MCC (Matthews Correlation Coefficient) as your primary metric — it's what Kaggle used for this competition and it's appropriate for severe imbalance. Include a dummy baseline (predict all negative) to anchor the comparison, and show that accuracy is a useless metric here.

**04_evaluation.ipynb** — Go deep on the best model. Precision-recall curves are more informative than ROC for this task. SHAP values let you tell a story about which production stations contribute most to defects — this is narratively satisfying and shows you understand the domain. Threshold sensitivity analysis (how precision/recall trade off as you move the decision boundary) is also worth including.

**05_business_impact.ipynb** — This is what separates a good portfolio project from a great one. Define two costs: the cost of a false positive (scrapping a good part) and the cost of a false negative (a defective part reaching the customer). Even if you use hypothetical numbers, framing the model output in terms of euros saved per shift is what resonates with hiring managers outside ML roles. You can show how the optimal threshold shifts depending on the cost ratio.

**shared/** — Keep a `config.py` with paths and hyperparameters, a `utils.py` with reusable plotting functions, and a solid `README.md` that walks someone through running everything top to bottom.

One additional suggestion: consider a short **`00_overview.ipynb`** that acts as an executive summary — one cell per notebook, showing the key output (a chart, a metric, a table). This makes it easy for someone to skim your work quickly, which matters on Kaggle profiles and GitHub.
