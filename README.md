# Multimodal Sit-to-Walk Aging Analysis (`multimodal-sit-to-walk-aging`)

**Multimodal Sensor Fusion and Normative Anomaly Detection for Characterizing Age-Associated Movement Pattern Variation in Sit-to-Walk Transitions.**

---

## 📌 Overview

This repository contains the notebook-based analysis framework for:
1. **Multimodal Sensor Fusion:** Evaluating sEMG, AMG, and kinematic feature contributions for lower-limb movement classification.
2. **Normative Anomaly Detection:** Isolation Forest model training on healthy controls to quantify age-associated functional movement variation.

---

## 📊 Dataset Links & Usage Methodology

### 1. Dataset Source Links
- **Hugging Face Hub Repository**: [`Tdongxu/A_Synchronized_Lower_Limb_AMG_sEMG_and_Mocap`](https://huggingface.co/datasets/Tdongxu/A_Synchronized_Lower_Limb_AMG_sEMG_and_Mocap)
- **Monash University Biomechanics Dataset**: Dataset ID `24515092`

### 2. Dataset Overview
The dataset contains synchronized multimodal sensor recordings collected across **95 human participants (805 total trials)** performing lower-limb functional movements (Deadlift, Deep Squat, Forward Lunge, Stair Ascent, and Sit-to-Walk transitions). It records three synchronized biomedical modalities:
- **Surface Electromyography (sEMG)**: 8 lower-limb muscle channels (Rectus Femoris, Vastus Lateralis/Medialis, Semitendinosus, Biceps Femoris, Tibialis Anterior, Gastrocnemius Medialis/Lateralis) alongside Maximum Voluntary Contraction (MVC) calibration trials.
- **Acoustic Myography (AMG)**: Muscle acoustic signals processed with 5 Hz high-pass filtering.
- **Motion Capture (MoCap) Kinematics & IMUs**: 3D joint angles (degrees) and tri-axial IMU accelerations.

Participants are categorized into three age cohorts:
- **Young Normative Cohort**: N=27 (Age 18–35)
- **Middle-Aged Cohort**: N=20 (Age 36–59)
- **Older Adult Cohort**: N=18 (Age 60+)

### 3. How We Used the Dataset
- **%MVC Normalization & 14D Feature Extraction ([`01_dataset_processing.ipynb`](notebooks/01_dataset_processing.ipynb))**:
  - Parsed quadriceps, hamstrings, and shank MVC trials to extract muscle peak voltages.
  - Normalized sEMG amplitude into percentage of Maximum Voluntary Contraction (%MVC).
  - Derived 14 distribution features per trial (RMS, std, p25, p75, p95) across sEMG, AMG, and joint angle/IMU kinematics.
- **Multimodal Modality Ablation ([`02_multimodal_ablation.ipynb`](notebooks/02_multimodal_ablation.ipynb))**:
  - Applied 5-fold participant-disjoint cross-validation across 30 control participants.
  - Evaluated Random Forest classifiers on single-modality feature subsets (sEMG-only, AMG-only, Kinematics-only) vs combined early-fusion feature vectors.
- **Normative Anomaly Detection & Age Trajectory ([`03_normative_aging_analysis.ipynb`](notebooks/03_normative_aging_analysis.ipynb))**:
  - Fit an Isolation Forest model ($N=300$ trees, contamination=0.05) exclusively on young training controls ($N=18$).
  - Frozen threshold ($\tau$) evaluated held-out young, middle-aged, and older adult cohorts across 100 random resamples.
  - Built a Covariate-Adjusted Linear Mixed-Effects Model (LMM) adjusting for age, sex, weight, and height to evaluate age-associated decline.
- **Baseline Inference & Neuromuscular Assessment ([`04_baseline_inference.ipynb`](notebooks/04_baseline_inference.ipynb))**:
  - Utilized serialized normative baseline weights (`models/healthy_normative_baseline.joblib`) to classify unseen trial executions and compute Neuromuscular Harmony scores.

---

## 📁 Repository Structure

```text
.
├── .gitignore                              # Git exclusion rules
├── README.md                               # Project documentation
├── requirements.txt                        # Core Python dependencies
├── notebooks/                              # Jupyter analysis notebooks
│   ├── 01_dataset_processing.ipynb         # Dataset loading & %MVC feature extraction
│   ├── 02_multimodal_ablation.ipynb        # Modality ablation & exercise classification
│   ├── 03_normative_aging_analysis.ipynb   # Isolation Forest anomaly detection & LMM trajectory
│   └── 04_baseline_inference.ipynb         # Pre-trained baseline model inference
├── data/                                   # Processed feature matrices & result tables
│   ├── master_dataset_index.csv
│   ├── monash_14d_features.csv
│   ├── exp1_ablation_results.csv
│   └── exp2_aging_ablation_results.csv
└── models/                                 # Pre-trained model checkpoints
    ├── healthy_normative_baseline.joblib
    └── healthy_normative_baseline.pkl
```

---

## ⚡ Quick Start

### 1. Installation

```bash
git clone https://github.com/slyvarun/normative-movement-anomaly-detection.git
cd normative-movement-anomaly-detection

# (Optional) Create virtual environment
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Running Notebooks

Launch Jupyter Lab or Jupyter Notebook:

```bash
jupyter lab
```

Run notebooks in sequential order:
1. `notebooks/01_dataset_processing.ipynb`
2. `notebooks/02_multimodal_ablation.ipynb`
3. `notebooks/03_normative_aging_analysis.ipynb`
4. `notebooks/04_baseline_inference.ipynb`

---

## 📜 License

This project is licensed under the MIT License.
