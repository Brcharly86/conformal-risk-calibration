# Lessons Learned - Credit Risk Modeling

## 📚 Technical Lessons

### 1. Environment & Setup

**Lesson:** Setting up the environment correctly is half the work.

**Challenges encountered:**
- Python 32-bit on Mac → XGBoost wouldn't work
- MAPIE 1.4.1 API changed → manual implementation required
- LightGBM required `libomp` → additional installation needed

**Solution:** Use Python 64-bit, implement Conformal Prediction manually, install `brew install libomp`.

---

### 2. EDA (Exploratory Data Analysis)

**Lesson:** EDA is not just visualization; it's understanding the business.

**Key findings:**
- `PAY_0` is the most important predictor (current payment status)
- Age and billing amounts have low predictive power
- Imbalanced dataset (78% No default, 22% Default)

**Takeaway:** Not all variables are useful. Focus on what truly matters.

---

### 3. Modeling

**Lesson:** XGBoost is excellent, but requires balancing.

**Challenges encountered:**
- Base model had low recall (60%)
- Extreme class imbalance affected performance

**Solution:** Use `scale_pos_weight` and SMOTE to balance classes.

---

### 4. Conformal Prediction

**Lesson:** Uncertainty is as important as the prediction itself.

**Key learnings:**
- Conformal Prediction works perfectly with XGBoost
- Empirical coverage (89.7%) almost identical to theoretical (90%)
- Average set size (1.38) is excellent

**Takeaway:** Always quantify uncertainty, especially in risk applications.

---

### 5. Optimization

**Lesson:** GridSearchCV + SMOTE improve the model, but are not magic.

**Challenges encountered:**
- GridSearchCV is slow with many parameters
- SMOTE can over-balance classes

**Solution:** Use RandomizedSearchCV for large grids, adjust SMOTE's `sampling_strategy`.

---

### 6. Threshold & Costs

**Lesson:** The optimal threshold depends on the cost of FN vs FP.

**Key learnings:**
- Low threshold = high recall, low precision
- High threshold = low recall, high precision
- The FN/FP ratio determines the optimal threshold
- **There is no universal threshold. It depends on the business.**

**Takeaway:** Always align model decisions with business costs.

---

### 7. Segmentation

**Lesson:** One threshold does not fit all customers.

**Key learnings:**
- Low-risk clients → high threshold (approve them)
- High-risk clients → low threshold (reject them)
- Segmentation improved accuracy from 40% to 73%

**Takeaway:** Segment customers by risk profile for better business outcomes.

---

## 🧪 Hypotheses

### Hypothesis 1: "Feature Engineering would improve the model"
**Result:** ✅ Confirmed. New features like `PAY_RATIO`, `DEBT_RATIO`, and `HAS_DELAY` improved performance.

### Hypothesis 2: "Risk-based segmentation improves the balance"
**Result:** ✅ Confirmed. Segmentation improved accuracy from 40% to 73% and precision from 26% to 42%.

### Hypothesis 3: "A low threshold is always better for business"
**Result:** ❌ Refuted. Low threshold (0.10) improves recall but reduces precision and accuracy. The optimal choice depends on the risk profile.

### Hypothesis 4: "FN cost is more important than FP cost"
**Result:** ✅ Confirmed. With FN > FP (10:1 ratio), the model chooses a low threshold to minimize FN.

---

## 📈 Decisions Made

| Decision | Why |
|----------|-----|
| **Use XGBoost** | Best performance on tabular data with imbalance |
| **SMOTE 0.8** | Balances without over-balancing |
| **Threshold 0.10 for High Risk** | Minimizes FN in risky clients |
| **Threshold 0.50 for Low Risk** | Maximizes approvals for safe clients |
| **Manual Conformal Prediction** | MAPIE API changed in 1.4.1 |
| **Segmentation** | Improves overall accuracy and precision |

---

## 🔬 What Would I Do Differently?

1. **More data** → Higher predictive power
2. **Deeper Feature Engineering** → Interaction variables
3. **Ensemble models** → XGBoost + LightGBM + RandomForest
4. **Real costs** → Use historical business data
5. **Out-of-time validation** → Verify temporal stability

---

## 💡 Tips for Future Projects

1. **Set up environment first** → Avoid dependency issues
2. **Deep EDA** → Understand data before modeling
3. **Optimize by cost, not accuracy** → Align model with business
4. **Segment** → One threshold does not fit all
5. **Document everything** → Future you will thank you

---

## 🎯 Key Takeaways

| Concept | Lesson |
|---------|--------|
| **Model Selection** | XGBoost is robust for imbalanced tabular data |
| **Class Imbalance** | SMOTE + `scale_pos_weight` is a winning combination |
| **Uncertainty** | Conformal Prediction provides reliable coverage |
| **Threshold** | Optimize by business cost, not statistical metrics |
| **Segmentation** | Different clients need different thresholds |
| **Economic Capital** | Reserve funds to cover expected losses |

---

## 📊 Final Model Performance

| Metric | Base Model | Segmented Model | Improvement |
|--------|------------|-----------------|-------------|
| Accuracy | 39.72% | **72.87%** | **+33.15%** |
| Recall | 93.07% | 62.25% | -30.82% |
| Precision | 25.95% | **42.29%** | **+16.35%** |
| F1-Score | 0.406 | **0.504** | **+0.098** |

### Business Impact

| Model | Approved Clients | Rejected Clients | Defaults Detected | Total Cost |
|-------|------------------|------------------|-------------------|------------|
| **Base (0.10)** | 1,148 (19%) | 4,852 (81%) | 1,235 (93%) | $444,500 |
| **Segmented** | **3,546 (59%)** | **2,454 (41%)** | 826 (62%) | **~$350,000** |

---

*📅 Last updated: July 2026*