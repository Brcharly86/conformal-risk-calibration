# Credit Risk Modeling - Executive Report

## 📋 Executive Summary

This project develops a **credit default prediction model** using **XGBoost** with **Conformal Prediction** for uncertainty quantification. The goal is to help financial institutions make credit approval decisions by balancing **risk** (undetected defaults) and **growth** (incorrectly rejected clients).

### Key Results

| Metric | Base Model | Segmented Model | Improvement |
|--------|------------|-----------------|-------------|
| **Accuracy** | 39.72% | **72.87%** | **+33.15%** |
| **Recall** | 93.07% | 62.25% | -30.82% |
| **Precision** | 25.95% | **42.29%** | **+16.35%** |
| **F1-Score** | 0.406 | **0.504** | **+0.098** |

### Business Impact

| Model | Approved Clients | Rejected Clients | Defaults Detected | Total Cost |
|-------|------------------|------------------|-------------------|------------|
| **Base (Threshold 0.10)** | 1,148 (19%) | 4,852 (81%) | 1,235 (93%) | $444,500 |
| **Segmented** | **3,546 (59%)** | **2,454 (41%)** | 826 (62%) | **~$350,000** |

**Conclusion:** The segmented model approves **3x more clients** while reducing total cost by **~$94,500**.

---

## 🔍 Methodology

### 1. Exploratory Data Analysis (EDA)

- **Dataset:** UCI Credit Card Default (30,000 records, 24 features)
- **Target:** `default` (22.1% default, 77.9% non-default)
- **Key finding:** `PAY_0` (current payment status) is the most important predictor

### 2. Base Model

- **Algorithm:** XGBoost with class balancing (`scale_pos_weight=3.5`)
- **Performance:** 76.5% accuracy, 60% recall
- **AUC-ROC:** 0.775

### 3. Conformal Prediction

- **Method:** Split Conformal Classifier (manual implementation)
- **Coverage:** 89.7% empirical vs 90% theoretical
- **Average set size:** 1.38 classes

### 4. Optimization

- **GridSearchCV:** Hyperparameter tuning
- **SMOTE:** Advanced class balancing
- **Feature Importance:** Identified top predictors
- **Threshold Tuning:** Optimized by business costs

### 5. Segmentation

- **Strategy:** Different thresholds by risk segment
- **Segments:** Low Risk (0.50), Medium Risk (0.30), High Risk (0.10)
- **Result:** Accuracy improved from 40% to 73%

---

## 📊 Detailed Results

### Model Performance Comparison

| Metric | Base Model | Segmented Model | Improvement |
|--------|------------|-----------------|-------------|
| **Accuracy** | 39.72% | **72.87%** | **+33.15%** |
| **Recall** | 93.07% | 62.25% | -30.82% |
| **Precision** | 25.95% | **42.29%** | **+16.35%** |
| **F1-Score** | 0.406 | **0.504** | **+0.098** |
| **AUC-ROC** | 0.775 | 0.757 | -0.018 |

### Confusion Matrix Comparison

#### Base Model (Threshold 0.10)

| | Pred. No Default | Pred. Default |
|---|-----------------|---------------|
| **Actual No Default** | 1,148 | 3,525 |
| **Actual Default** | 92 | 1,235 |

#### Segmented Model

| | Pred. No Default | Pred. Default |
|---|-----------------|---------------|
| **Actual No Default** | 3,546 | 1,127 |
| **Actual Default** | 501 | 826 |

### Business Impact Analysis

| Model | Approved Clients | Rejected Clients | Defaults Detected | Total Cost |
|-------|------------------|------------------|-------------------|------------|
| **Base (Threshold 0.10)** | 1,148 (19%) | 4,852 (81%) | 1,235 (93%) | $444,500 |
| **Segmented** | **3,546 (59%)** | **2,454 (41%)** | 826 (62%) | **~$350,000** |

---

## 💰 Economic Capital Analysis

### Losses by Segment

| Segment | FN | FP | FN Loss | FP Loss | Economic Capital |
|---------|----|----|---------|---------|------------------|
| Low Risk | 300 | 0 | $60,000 | $0 | $60,000 |
| Medium Risk | 320 | 320 | $160,000 | $32,000 | $192,000 |
| High Risk | 0 | 0 | $0 | $0 | $0 |
| **TOTAL** | **620** | **320** | **$220,000** | **$32,000** | **$252,000** |

### Scenario Analysis

| Scenario | Clients Approved | Revenue | Capital Reserve | Net Profit |
|----------|------------------|---------|-----------------|------------|
| **Optimistic** | 4,380 | $2,628,000 | $176,400 | $2,451,600 |
| **Realistic** | 4,380 | $2,190,000 | $252,000 | $1,938,000 |
| **Pessimistic** | 4,380 | $1,752,000 | $327,600 | $1,424,400 |

**Conclusion:** The model is profitable even in pessimistic scenarios.

---

## 💡 Key Decisions and Justification

| Decision | Justification |
|----------|---------------|
| **Use XGBoost** | Best performance on tabular data with class imbalance |
| **SMOTE 0.8** | Balances without over-synthesizing minority class |
| **Threshold 0.10 for High Risk** | Minimizes FN (expensive) in risky clients |
| **Threshold 0.50 for Low Risk** | Maximizes approvals for safe clients |
| **Segmentation** | Improves overall accuracy and precision |

---

## 🚧 Challenges Encountered

| Problem | Solution |
|---------|----------|
| **Python 32-bit on Mac** | Installed Python 64-bit via Homebrew |
| **XGBoost import issues** | Implemented Conformal Prediction manually |
| **LightGBM not working** | Installed `libomp` via Homebrew |
| **MAPIE 1.4.1 API changes** | Manual Conformal Prediction implementation |
| **KeyError 'PAY_1'** | Used only existing columns (`PAY_0`, `PAY_2`, `PAY_3`, etc.) |
| **KeyError 'Medio'** | Added missing segment to threshold dictionary |

---

## 🧪 Hypotheses and Findings

| Hypothesis | Result | Confidence |
|------------|--------|------------|
| Feature Engineering improves the model | ✅ Confirmed | High |
| Risk-based segmentation improves balance | ✅ Confirmed | High |
| Low threshold is always better for business | ❌ Refuted | High |
| FN cost is more important than FP cost | ✅ Confirmed | Medium |
| Segmentation improves overall performance | ✅ Confirmed | High |

---

## 🎯 Recommendations

### By Business Profile

| Profile | Threshold | Recall | Precision | Use Case |
|---------|-----------|--------|-----------|----------|
| **🛡️ Conservative** | 0.05-0.15 | 90-98% | 23-26% | Traditional banks, regulated institutions |
| **⚖️ Balanced** | 0.25-0.35 | 70-75% | 38-42% | Established fintechs |
| **🚀 Liberal** | 0.45-0.50 | 48-55% | 55-60% | Growth-focused fintechs |

### General Recommendations

1. **Start with a "Balanced" approach** (threshold ~0.30)
2. **Monitor results for 3-6 months** before adjusting
3. **Adjust based on actual portfolio performance**
4. **Not all defaults are equal** - segment by exposure amount

---

## 📊 Key Insights

### What We Learned

1. **The business context matters more than pure accuracy**
   - A model with 93% recall but 26% precision can still be optimal depending on costs

2. **Segmentation is underrated**
   - One threshold doesn't work for all clients
   - Segmenting by risk profile drastically improves business outcomes

3. **Uncertainty quantification is essential in risk**
   - Conformal Prediction provides calibrated uncertainty
   - Helps decision-makers understand when to trust predictions

4. **Economic capital is a key business concept**
   - Reserving funds for expected losses makes the model sustainable
   - Even with 620 false negatives, the business can still be profitable

### What I Would Do Differently

1. **Start with costs, not accuracy** - Define business costs before building the model
2. **Explore more features** - Interaction variables and trend indicators
3. **Try ensemble methods earlier** - XGBoost + LightGBM + RandomForest
4. **Validate with out-of-time data** - Test model stability over time

---

## 📁 Files Generated

| File | Description |
|------|-------------|
| `notebooks/01_eda.ipynb` | Exploratory Data Analysis |
| `notebooks/02_modeling_base.ipynb` | Base model with Conformal Prediction |
| `notebooks/03_modeling_optimized.ipynb` | Optimized model with segmentation |
| `outputs/figures/default_distribution.png` | Default distribution visualization |
| `outputs/figures/age_distribution.png` | Age distribution by default |
| `outputs/figures/credit_limit_distribution.png` | Credit limit analysis |
| `outputs/figures/correlation_matrix.png` | Correlation matrix heatmap |
| `outputs/figures/feature_importance.png` | Top 10 features |
| `outputs/figures/threshold_tuning.png` | Threshold optimization analysis |
| `outputs/figures/matriz_confusion.png` | Confusion matrix comparison |
| `outputs/figures/roc_curve_base.png` | ROC curve analysis |
| `outputs/figures/cobertura_analisis.png` | Coverage analysis |

---

## 📚 References

1. **Dataset:** UCI Machine Learning Repository
   - https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients

2. **Conformal Prediction:**
   - Shafer, G., & Vovk, V. (2008). A tutorial on conformal prediction.
   - https://arxiv.org/abs/0706.3188

3. **XGBoost Documentation:**
   - https://xgboost.readthedocs.io/

4. **SMOTE:**
   - Chawla, N. V., et al. (2002). SMOTE: Synthetic Minority Over-sampling Technique.
   - https://arxiv.org/abs/1106.1813

5. **Economic Capital in Banking:**
   - Basel Committee on Banking Supervision (2004). Basel II framework.

---

## ✅ Conclusion

### Final Verdict

> **The segmented credit risk model is viable and profitable for business use.**

**Why?**

1. ✅ **Accuracy improved from 39.72% to 72.87%** (+33.15%)
2. ✅ **Precision improved from 25.95% to 42.29%** (+16.35%)
3. ✅ **Business cost reduced from $444,500 to ~$350,000** (-$94,500 | 21.26 %)
4. ✅ **Client approvals increased from 19% to 59%** (+40%)
5. ✅ **Model remains profitable even in pessimistic scenarios**

### Key Takeaways

| Concept | Lesson |
|---------|--------|
| **Model Selection** | XGBoost is robust for imbalanced tabular data |
| **Class Imbalance** | SMOTE + `scale_pos_weight` is a winning combination |
| **Uncertainty** | Conformal Prediction provides reliable coverage |
| **Threshold** | Optimize by business cost, not statistical metrics |
| **Segmentation** | Different clients need different thresholds |
| **Economic Capital** | Reserve funds to cover expected losses |

---

## 📅 Revision History

| Date | Changes |
|------|---------|
| July 2026 | Initial version - English translation |
| July 2026 | Added Economic Capital analysis |
| July 2026 | Added segmentation results |
| July 2026 | Added lessons learned and hypotheses |

---

*📅 Last updated: July 2026*

---

**📬 Contact**

**Carlos Bloomer-Reeve**

- **LinkedIn:** [linkedin.com/in/cabloomer-reeve](www.linkedin.com/in/cabloomer-reeve)
- **Email:** br.charly@gmail.com.com
- **GitHub:** [github.com/Brcharly86](https://github.com/Brcharly86)

---

⭐ **If you find this report useful, please star the repository!** ⭐