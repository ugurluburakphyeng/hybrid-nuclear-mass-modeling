# Hybrid Nuclear Mass and Uncertainty Modeling Architecture

A computational nuclear-physics project investigating whether machine-learning models can learn systematic residual structure left by the Semi-Empirical Mass Formula (SEMF), with particular attention to nuclear shell-related features and predictive uncertainty.

> **Project status:** Phases 1–9 completed. Physical interpretation, literature benchmarking, and final scientific reporting are planned as subsequent phases.

---

## 1. Project Overview

The Semi-Empirical Mass Formula (SEMF) provides a macroscopic description of nuclear binding energies but does not explicitly capture several microscopic nuclear-structure effects, including shell structure.

This project investigates the residual

[
R = B_{\mathrm{exp}} - B_{\mathrm{SEMF}}
]

rather than attempting to replace the SEMF directly.

The central idea is:

> **Can machine-learning models learn systematic structure in the residuals of a macroscopic nuclear mass model using physically motivated nuclear features?**

The project uses experimentally evaluated nuclear data and compares several machine-learning approaches under controlled cross-validation, ablation, robustness, and hyperparameter-optimization experiments.

---

## 2. Research Objectives

The current project has five main objectives:

1. Establish a SEMF residual baseline.
2. Determine whether ML models can learn systematic structure in the SEMF residuals.
3. Test whether physically motivated shell-related features improve predictive performance.
4. Evaluate whether the observed performance is robust to random seeds and model choice.
5. Quantify the effect of hyperparameter optimization and predictive uncertainty.

Future phases will extend the analysis toward physical interpretation and literature benchmarking.

---

## 3. Dataset

The current analysis contains:

* **3,555 nuclei**
* Proton number: (Z)
* Neutron number: (N)
* Mass number: (A)
* Experimentally derived nuclear binding-energy information
* SEMF-derived binding-energy estimates
* ML target: SEMF residual

The primary target variable is:

[
R = B_{\mathrm{exp}} - B_{\mathrm{SEMF}}
]

The project uses nuclear-physics-motivated feature engineering rather than treating (Z), (N), and (A) as purely arbitrary numerical inputs.

---

## 4. Feature Engineering

Several physically motivated features were investigated.

### Basic features

* (Z)
* (N)
* (A)
* neutron excess
* proton parity
* neutron parity

### Shell-related features

* distance of (Z) from the nearest magic number
* distance of (N) from the nearest magic number
* signed neutron valence distance

The magic numbers considered are:

[
2,\ 8,\ 20,\ 28,\ 50,\ 82,\ 126
]

These features were evaluated explicitly through ablation experiments rather than assuming that their inclusion necessarily improves the model.

---

# 5. Project Phases

## Phase 1 — Baseline and Residual Isolation

The SEMF was established as the physical baseline.

The experimental binding energy was compared with the SEMF prediction to isolate the residual structure.

The resulting residuals were then examined across the nuclear (N-Z) plane.

---

## Phase 2 — Residual Analysis

The residual distribution and spatial structure of the SEMF error were investigated.

The purpose of this stage was to determine whether the SEMF residuals contain systematic structure that could potentially be learned by a statistical model.

---

## Phase 3 — Deformation Data Integration

Nuclear deformation information was incorporated using FRDM-derived deformation parameters.

The deformation parameter (\beta_2) was investigated as an additional physically motivated representation of nuclear structure.

---

## Phase 4 — Deformation Analysis

Different representations of deformation were investigated, including:

* signed (\beta_2)
* (|\beta_2|)
* (\beta_2^2)
* combined deformation representations

The analysis examined whether deformation information contributes to residual prediction.

---

## Phase 5 — Hybrid Modeling

Machine-learning models were introduced to predict the SEMF residual rather than the absolute binding energy.

The main models investigated included:

* Random Forest
* Extra Trees
* Gradient Boosting
* Gaussian Process Regression

Cross-validation was used to estimate predictive performance.

---

# 6. Phase 6 — Full Performance Analysis

The complete dataset contained:

**3,555 nuclei**

The SEMF baseline produced:

| Model             |           RMSE |
| ----------------- | -------------: |
| SEMF              | **5.5603 MeV** |
| Random Forest     | **0.8117 MeV** |
| Extra Trees       | **0.7327 MeV** |
| Gradient Boosting | **0.7769 MeV** |
| GPR               | **0.9837 MeV** |

The best tree-based model in the initial comparison was **Extra Trees**.

Its 5-fold cross-validation RMSE was:

**0.7327 ± 0.2463 MeV**

The GPR experiment additionally provided predictive uncertainty estimates, with a mean predictive standard deviation of approximately:

**0.6338 MeV**

---

# 7. Phase 7 — Critical Role of Magic-Number Features

An ablation study was performed to determine whether shell-related features provide measurable predictive information.

Three feature groups were compared:

### Geometry

[
(Z,N,A)
]

RMSE:

**0.9417 ± 0.3327 MeV**

### Geometry + Basic

Geometry plus neutron excess and parity:

**0.7164 ± 0.2676 MeV**

### Full Shell Model

Geometry + basic features + shell-related features:

**0.7327 ± 0.2463 MeV**

The incremental result was:

| Transition       |           ΔRMSE |
| ---------------- | --------------: |
| SEMF → Geometry  |     +4.6186 MeV |
| Geometry → Basic |     +0.2253 MeV |
| Basic → Shell    | **−0.0164 MeV** |

Importantly, the shell features did **not** produce a consistent improvement in the initial experiment.

This result is treated as a scientific finding to be investigated rather than being interpreted as evidence that shell structure is unimportant.

---

# 8. Phase 8 — Robustness Analysis

The Basic and Shell feature sets were tested under multiple random seeds and different ML algorithms.

## Random-seed analysis

Across 10 random seeds:

| Model |               Mean RMSE |
| ----- | ----------------------: |
| Basic | **0.6651 ± 0.0420 MeV** |
| Shell | **0.6761 ± 0.0398 MeV** |

The Basic model achieved the lower RMSE in:

**6 / 10 seeds**

while the Shell model was better in:

**4 / 10 seeds**

Mean difference:

**Basic − Shell = −0.0110 MeV**

---

## Algorithm robustness

The same feature comparison was repeated using different algorithms.

| Algorithm         |      Basic RMSE |      Shell RMSE | Winner |
| ----------------- | --------------: | --------------: | ------ |
| Random Forest     | 0.8037 ± 0.2323 | 0.8117 ± 0.1999 | Basic  |
| Extra Trees       | 0.7164 ± 0.2676 | 0.7327 ± 0.2463 | Basic  |
| Gradient Boosting | 0.7396 ± 0.3034 | 0.7769 ± 0.2686 | Basic  |

The robustness experiments therefore did not provide evidence for a consistent predictive advantage from the current shell-feature representation.

---

# 9. Phase 9 — Hyperparameter Optimization

Grid-search optimization was performed for the main tree-based models.

For Extra Trees, the search covered:

* number of estimators
* maximum tree depth
* minimum samples per leaf

The optimized Extra Trees model obtained:

**5-fold CV RMSE = 0.7084 MeV**

with:

```text
max_depth = 30
min_samples_leaf = 1
n_estimators = 500
```

Compared with the previous Extra Trees result:

```text
Default:   0.7164 MeV
Optimized: 0.7084 MeV
```

The improvement was:

**0.0080 MeV**

This indicates that hyperparameter optimization provided a measurable but relatively small improvement over the default configuration.

---

# 10. Current Main Result

At the current stage, the strongest optimized result is:

> **Extra Trees — 0.7084 MeV 5-fold CV RMSE**

compared with the SEMF baseline:

> **SEMF — 5.5603 MeV RMSE**

The optimized ML model therefore substantially reduces the prediction error of the SEMF residual under the current cross-validation setup.

However, the numerical improvement should not be interpreted as a direct replacement of established nuclear mass models without further validation.

---

# 11. Current Scientific Interpretation

The current results suggest that a substantial amount of systematic structure remains in the SEMF residuals and can be modeled using nuclear-number-based features.

An important result is that the explicitly constructed shell-related features did not consistently improve predictive performance over the Basic feature set.

This motivates further investigation into:

* the physical meaning of the learned residual structure,
* the relationship between residuals and shell closures,
* deformation and shell effects,
* regions of the nuclear chart where the model performs poorly,
* and whether the chosen shell-feature representation adequately captures microscopic nuclear structure.

These questions form the basis of the next project phases.

---

# 12. Planned Future Work

## Phase 10 — Physical Interpretation

The next stage will investigate the physical origin of the learned residual structure.

Planned analyses include:

* residual maps in the (N-Z) plane,
* performance near magic numbers,
* light versus heavy nuclei,
* residual behavior near shell closures,
* high-error nuclei,
* regional performance,
* interpretation of feature contributions.

The goal is to connect the statistical results to nuclear-physics mechanisms.

---

## Phase 11 — Literature Benchmark

The model will be compared with relevant nuclear mass-model and machine-learning literature.

Comparisons will account for differences in:

* dataset,
* target definition,
* train/test methodology,
* cross-validation strategy,
* reported metric,
* and physical model assumptions.

The objective is to establish where the present approach sits relative to existing methods rather than simply reporting the lowest numerical RMSE.

---

## Phase 12 — Scientific Report and Reproducibility

The final stage will consolidate the project into a scientific report covering:

1. Physical motivation
2. Theoretical background
3. Dataset
4. SEMF baseline
5. Residual formulation
6. Feature engineering
7. ML methodology
8. Ablation studies
9. Robustness analysis
10. Hyperparameter optimization
11. Physical interpretation
12. Literature benchmark
13. Limitations
14. Conclusions
15. Reproducibility

---

# 13. Limitations

The current results should be interpreted within the following limitations:

* The ML models are trained on the available nuclear dataset.
* Random cross-validation does not necessarily represent extrapolation to unknown regions of the nuclear chart.
* The current shell-feature representation may not capture the full microscopic shell structure.
* Different published nuclear mass models may use different datasets and evaluation protocols.
* A low residual-prediction error does not by itself establish a new physical theory.

These limitations will be investigated further in the final analysis.

---

# 14. Reproducibility

The project is being developed using Python and standard scientific-computing and machine-learning libraries.

Main libraries include:

* NumPy
* Pandas
* scikit-learn
* Matplotlib

Random seeds and cross-validation settings are explicitly controlled where applicable.

The notebooks document the individual analysis phases and are intended to allow the computational experiments to be reproduced.

---

# 15. Project Status

### Completed

* [x] Data preparation
* [x] SEMF baseline
* [x] Residual isolation
* [x] Feature engineering
* [x] Deformation analysis
* [x] Hybrid ML modeling
* [x] Model comparison
* [x] Magic-number ablation
* [x] Random-seed robustness
* [x] Algorithm robustness
* [x] Hyperparameter optimization

### In progress / planned

* [ ] Physical interpretation
* [ ] Detailed regional N-Z analysis
* [ ] Literature benchmark
* [ ] Final scientific report
* [ ] Final reproducibility package

---

## 16. Research Direction

The central research question of the project is:

> **To what extent can machine learning recover systematic nuclear-structure information contained in the residuals of a macroscopic nuclear mass model, and can this learned structure be physically interpreted?**

The goal is not simply to minimize RMSE, but to investigate whether the residual structure contains physically meaningful information that can be identified, tested, and interpreted.

---

## 17. Disclaimer

This repository represents an ongoing independent computational research project.

The reported results correspond to the current experimental setup and should not be interpreted as a definitive replacement for established nuclear mass models.

Further physical analysis and literature benchmarking are required before drawing broader scientific conclusions.
