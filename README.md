# Multimodal Sit-to-Walk Aging Analysis (`multimodal-sit-to-walk-aging`)

**Multimodal Sensor Fusion and Normative Anomaly Detection for Characterizing Age-Associated Movement Pattern Variation in Sit-to-Walk Transitions.**

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Hugging Face Dataset](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Dataset-blue)](https://huggingface.co/datasets/Tdongxu/A_Synchronized_Lower_Limb_AMG_sEMG_and_Mocap)

---

## 📌 Overview

This repository provides an open-source analysis framework for multimodal sensor fusion, normative anomaly detection, and neuromuscular aging characterization during functional lower-limb movements (specifically Sit-to-Walk transitions).

### Core Research Contributions:
1. **Multimodal Sensor Fusion & Modality Ablation**: Systematic evaluation of surface Electromyography (sEMG), Acoustic Myography (AMG), and Kinematic (MoCap & IMU) feature contributions for functional movement classification.
2. **Normative Anomaly Detection**: Isolation Forest model trained exclusively on young healthy controls ($N=18$) to quantify age-associated functional movement variation as an outlier score.
3. **Three-Cohort Biomechanical Trajectory**: Monotonic degradation assessment across Young ($N=27$), Middle-Aged ($N=20$), and Older Adult ($N=18$) cohorts with FDR-corrected non-parametric statistics and effect size analysis.
4. **Covariate-Adjusted Linear Mixed-Effects Model (LMM)**: Statistical validation adjusting for age, sex, weight, and height to isolate true age-related functional decline.

---

## 📊 Datasets & Experimental Setup

Our research framework incorporates **two distinct dataset sources**:

### 1. Monash University Biomechanics Dataset (Dataset ID `24515092`)
- **Origin**: Monash University Biomechanics Group.
- **Focus**: Functional Sit-to-Walk transitions across **65 human participants** categorized into 3 distinct age cohorts:
  - **Young Normative Cohort**: $N=27$ (Age 18–35)
  - **Middle-Aged Cohort**: $N=20$ (Age 36–59)
  - **Older Adult Cohort**: $N=18$ (Age 60+)
- **Recorded Modalities**: 3D Joint Kinematics (degrees) and tri-axial IMU accelerations ($g$).
- **Role in Study**: Used in **Experiment 2** for Normative Anomaly Detection (Isolation Forest) and Linear Mixed-Effects Model (LMM) aging trajectory analysis.

### 2. Hugging Face Multimodal Dataset ([`Tdongxu/A_Synchronized_Lower_Limb_AMG_sEMG_and_Mocap`](https://huggingface.co/datasets/Tdongxu/A_Synchronized_Lower_Limb_AMG_sEMG_and_Mocap))
- **Origin**: Independent repository hosted on Hugging Face Hub (Dongxu T. et al.).
- **Focus**: Synchronized multimodal lower-limb exercise recordings (Deadlift, Deep Squat, Forward Lunge, Stair Ascent, Sit-to-Walk).
- **Recorded Modalities**: 8 sEMG channels (%MVC normalized), Acoustic Myography (AMG, 5 Hz high-pass filtered), and Motion Capture (MoCap) Kinematics.
- **Role in Study**: Used in **Experiment 1** for 5-Fold GroupKFold Multimodal Modality Ablation benchmarking.

### 3. Processing Pipeline
- **%MVC Normalization & 14D Feature Extraction ([`01_dataset_processing.ipynb`](notebooks/01_dataset_processing.ipynb))**:
  - Calibrated sEMG signal amplitudes against subject-specific peak MVC voltages.
  - Extracted 14 distribution features per trial across sEMG, AMG, and IMU/Kinematic channels (RMS, Std, 25th/75th/95th percentiles, dynamic ROM).
- **Multimodal Modality Ablation ([`02_multimodal_ablation.ipynb`](notebooks/02_multimodal_ablation.ipynb))**:
  - Evaluated Random Forest classifiers across 5-fold participant-disjoint cross-validation (`GroupKFold` on `subject_id`).
- **Normative Anomaly Detection & Trajectory ([`03_normative_aging_analysis.ipynb`](notebooks/03_normative_aging_analysis.ipynb))**:
  - Isolation Forest model ($N_{\text{trees}}=300$, contamination=0.05) fit exclusively on young controls.
  - Frozen anomaly threshold ($\tau$) evaluated on held-out young, middle-aged, and older adult cohorts across 100 repeated resamples.
  - Fitted Covariate-Adjusted Linear Mixed-Effects Model (LMM) with participant random intercepts.
- **Pre-trained Baseline Inference ([`04_baseline_inference.ipynb`](notebooks/04_baseline_inference.ipynb))**:
  - Applied serialized model checkpoints (`models/healthy_normative_baseline.joblib`) to score unseen trial executions.

---

## 📈 Key Empirical Results

### 1. Multimodal Ablation Benchmark ($N=30$, 5-Fold GroupKFold)

| Modality / Sensor System | Category | Features ($D$) | Accuracy (%) | F1-Score (Macro) | Sensitivity (%) | Specificity (%) | ROC-AUC |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **sEMG-only** | Single | 5 | 32.92% ± 3.82% | 0.3159 | 32.92% | 77.64% | 0.5799 |
| **AMG-only** | Single | 3 | 55.42% ± 2.59% | 0.5399 | 55.42% | 85.14% | 0.8114 |
| **Kinematics-only** | Single | 6 | 66.88% ± 5.45% | 0.6624 | 66.88% | 88.96% | 0.8948 |
| **sEMG + AMG** | Dual | 8 | 53.54% ± 3.40% | 0.5246 | 53.54% | 84.51% | 0.8033 |
| **sEMG + Kinematics** | Dual | 11 | 66.04% ± 4.69% | 0.6549 | 66.04% | 88.68% | 0.8987 |
| **AMG + Kinematics** | Dual | 9 | 73.33% ± 1.41% | 0.7285 | 73.33% | 91.11% | 0.9149 |
| **Combined Multimodal (All 3)** | **Triple** | **14** | **75.42% ± 4.09%** | **0.7505** | **75.42%** | **91.81%** | **0.9222** |

*Combining sEMG, AMG, and Kinematic modalities (Triple-Modality Fusion) yields the highest classification accuracy (75.42%) and ROC-AUC (0.9222) across all 7 single, dual, and triple combinations.*

---

### 2. Three-Cohort Biomarker Progression ($N=65$)

| Feature Dimension | Young Controls ($N=27$) | Middle Cohort ($N=20$) | Older Cohort ($N=18$) | Raw $p$-value | FDR Adj. $p$ | Cliff's $\delta$ |
| :--- | :--- | :--- | :--- | :---: | :---: | :---: |
| **Dynamic ROM** (`angle_range`) | Median 178.73° [IQR 38.29°] | Median 151.48° [IQR 32.88°] | Median 126.71° [IQR 20.83°] | $2.71 \times 10^{-6}$ | $2.38 \times 10^{-5}$ | -0.84 (Large) |
| **Peak Accel** (`imu_accel_p95`) | Median 1.37 g [IQR 0.09 g] | Median 1.31 g [IQR 0.10 g] | Median 1.28 g [IQR 0.04 g] | $3.40 \times 10^{-6}$ | $2.38 \times 10^{-5}$ | -0.83 (Large) |
| **Accel Std** (`imu_accel_std`) | Median 0.16 g [IQR 0.05 g] | Median 0.14 g [IQR 0.04 g] | Median 0.12 g [IQR 0.02 g] | $1.73 \times 10^{-5}$ | $8.05 \times 10^{-5}$ | -0.77 (Large) |
| **Mean sEMG** (`semg_rms`) | Median 5.11% [IQR 2.99%] | Median 5.24% [IQR 1.93%] | Median 6.03% [IQR 2.17%] | 0.0321 | 0.0499 | +0.38 (Medium) |
| **Peak sEMG** (`semg_p95`) | Median 8.09% [IQR 3.26%] | Median 8.27% [IQR 3.16%] | Median 9.63% [IQR 3.71%] | 0.0476 | 0.0666 | +0.35 (Medium) |

---

### 3. Covariate-Adjusted Linear Mixed-Effects Model (LMM)

To ensure age-associated decline is not confounded by body morphology, we evaluated:
- **Unadjusted Model**: `normality_score ~ age + (1|subject_id)`
  - $\beta_{\text{age}} = -0.000954$ (95% CI: `[-0.001678, -0.000231]`), $p = 0.0097$
- **Covariate-Adjusted Model**: `normality_score ~ age + sex + weight + height + (1|subject_id)`
  - $\beta_{\text{age}} = -0.001017$ (95% CI: `[-0.001809, -0.000226]`), **$\mathbf{p = 0.0117}$** *(Statistically Significant)*

---

## 📁 Repository Structure

```text
.
├── .gitignore                              # Exclusion rules (docs/ & figures/ excluded)
├── README.md                               # Project documentation & benchmark summary
├── requirements.txt                        # Core Python dependencies
├── notebooks/                              # Analysis & modeling pipeline
│   ├── 01_dataset_processing.ipynb         # Feature extraction & %MVC normalization
│   ├── 02_multimodal_ablation.ipynb        # Single vs multimodal classification benchmark
│   ├── 03_normative_aging_analysis.ipynb   # Isolation Forest anomaly detection & LMM modeling
│   └── 04_baseline_inference.ipynb         # Model checkpoint scoring & inference
├── data/                                   # Processed datasets & experiment results
│   ├── master_dataset_index.csv
│   ├── monash_14d_features.csv
│   ├── exp1_ablation_results.csv
│   └── exp2_aging_ablation_results.csv
└── models/                                 # Serialized model checkpoints
    ├── healthy_normative_baseline.joblib
    └── healthy_normative_baseline.pkl
```

---

## ⚡ Quick Start

### 1. Environment Setup

```bash
# Clone the repository
git clone https://github.com/slyvarun/normative-movement-anomaly-detection.git
cd normative-movement-anomaly-detection

# Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Execution Order

Run the Jupyter notebooks sequentially to reproduce the findings:
1. `notebooks/01_dataset_processing.ipynb`
2. `notebooks/02_multimodal_ablation.ipynb`
3. `notebooks/03_normative_aging_analysis.ipynb`
4. `notebooks/04_baseline_inference.ipynb`

---

## 📜 License

This repository is distributed under the [MIT License](LICENSE).

