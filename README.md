# Credit Risk Modeling with Conformal Prediction

[![Python](https://img.shields.io/badge/Python-3.11-blue.svg)](https://www.python.org/)
[![XGBoost](https://img.shields.io/badge/XGBoost-2.0+-orange.svg)](https://xgboost.readthedocs.io/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## Overview

End-to-end credit default prediction pipeline using **XGBoost** with **Conformal Prediction** for uncertainty quantification. Optimized for business costs through threshold tuning and risk-based segmentation.

## Results

| Metric | Base Model | Segmented | Change |
|--------|------------|-----------|--------|
| Accuracy | 39.72% | 72.87% | +33.15% |
| Recall | 93.07% | 62.25% | -30.82% |
| Precision | 25.95% | 42.29% | +16.35% |
| F1-Score | 0.406 | 0.504 | +0.098 |

### Business Impact

| Model | Approved | Rejected | Defaults Detected | Cost |
|-------|----------|----------|-------------------|------|
| Base (th=0.10) | 1,148 (19%) | 4,852 (81%) | 1,235 (93%) | $444,500 |
| Segmented | 3,546 (59%) | 2,454 (41%) | 826 (62%) | ~$350,000 |

**The segmented model approves 3x more clients while reducing cost by ~$94,500.**

### Economic Capital

| Scenario | Revenue | Capital Reserve | Net Profit |
|----------|---------|-----------------|------------|
| Optimistic | $2,628,000 | $176,400 | $2,451,600 |
| Realistic | $2,190,000 | $252,000 | $1,938,000 |
| Pessimistic | $1,752,000 | $327,600 | $1,424,400 |

## Project Structure

```
conformal-risk-calibration/
├── docs/
│   ├── lessons_learned.md
│   └── project_notes.md
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_modeling_base.ipynb
│   └── 03_modeling_optimized.ipynb
├── outputs/
│   └── figures/
├── .gitignore
├── LICENSE
├── README.md
├── REPORT.md
└── requirements.txt
```

## Getting Started

```bash
git clone https://github.com/Brcharly86/conformal-risk-calibration.git
cd conformal-risk-calibration

python3.11 -m venv RiskCalibration
source RiskCalibration/bin/activate

pip install -r requirements.txt

# Mac users only
brew install libomp
```

**Run notebooks in order:**
1. `notebooks/01_eda.ipynb` – EDA
2. `notebooks/02_modeling_base.ipynb` – Base model + Conformal Prediction
3. `notebooks/03_modeling_optimized.ipynb` – Optimized + segmentation

## Methodology

1. **EDA** – UCI Credit Card Default dataset (30k records, 24 features). `PAY_0` identified as the strongest predictor.
2. **Base Model** – XGBoost with `scale_pos_weight=3.5`. Accuracy: 76.5%, Recall: 60%, AUC: 0.775.
3. **Conformal Prediction** – Manual split-conformal implementation. Empirical coverage: 89.7% (vs 90% theoretical).
4. **Optimization** – GridSearchCV, SMOTE, feature importance, cost-based threshold tuning.
5. **Segmentation** – Risk-based thresholds: Low (0.50), Medium (0.30), High (0.10). Accuracy improved from 40% to 73%.

## Key Takeaways

- **Business costs drive threshold selection**, not accuracy metrics alone.
- **Segmentation improves outcomes** – one threshold does not fit all clients.
- **Conformal Prediction provides calibrated uncertainty** for risk decisions.
- **Economic capital reserves** make the model sustainable even with expected losses.

## Technologies

- Python 3.11, Pandas, NumPy, Matplotlib, Seaborn
- Scikit-learn, XGBoost, SMOTE, MAPIE, Jupyter

## Documentation

- [Executive Report](REPORT.md)
- [Lessons Learned](docs/lessons_learned.md)
- [Project Notes](docs/project_notes.md)

## Contact

**Carlos A. Bloomer-Reeve**  
[LinkedIn](www.linkedin.com/in/cabloomer-reeve) · [GitHub](https://github.com/Brcharly86) · br.charly@gmail.com

## Quick Preview

![Default Distribution](outputs/figures/default_distribution.png)
![Feature Importance](outputs/figures/feature_importance.png)
![Threshold Analysis](outputs/figures/threshold_tuning.png)
![Confusion Matrix](outputs/figures/matriz_confusion.png)

---

*Last updated: July 2026*