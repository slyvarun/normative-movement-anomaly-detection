# Multimodal Sit-to-Walk Aging Analysis (`multimodal-sit-to-walk-aging`)

**Multimodal Sensor Fusion and Normative Anomaly Detection for Characterizing Age-Associated Movement Pattern Variation in Sit-to-Walk Transitions.**

---

## 📌 Overview

This repository contains the notebook-based analysis pipeline for:
1. **Multimodal Sensor Fusion:** Evaluating sEMG, AMG, and kinematic feature contributions for lower-limb movement classification.
2. **Normative Anomaly Detection:** Isolation Forest model training on healthy controls to quantify age-associated functional movement variation.

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
git clone https://github.com/your-username/multimodal-sit-to-walk-aging.git
cd multimodal-sit-to-walk-aging

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
