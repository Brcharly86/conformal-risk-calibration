# Project Notes - Credit Risk Modeling

## 📝 General Notes

### Project Objective
Develop a credit default prediction model with:
- High accuracy in default detection
- Uncertainty quantification (Conformal Prediction)
- Business cost optimization
- Segmentation for different client profiles

### Dataset
- **Source:** UCI Machine Learning Repository
- **Dataset:** Default of Credit Card Clients
- **Size:** 30,000 records, 24 features
- **Target:** `default` (0 = No default, 1 = Default)
- **Imbalance:** 77.9% No default, 22.1% Default

### Key Variables

| Variable | Meaning | Range |
|----------|---------|-------|
| `PAY_0` | Most recent payment status | -2 to 8 |
| `PAY_2` - `PAY_6` | Payment history (previous months) | -2 to 8 |
| `LIMIT_BAL` | Credit limit | 10,000 - 1,000,000 TWD |
| `AGE` | Client age | 21 - 79 years |
| `BILL_AMT1-6` | Monthly billing amount | Variable |
| `PAY_AMT1-6` | Monthly payment amount | Variable |

### PAY_* Values Interpretation
- `-2`: No consumption (didn't use the card)
- `-1`: Paid on time
- `0`: Paid minimum amount
- `1-8`: Delay in months (1 = 1 month delay, etc.)

---

## 🤔 Questions & Reflections

### 1. Why is PAY_0 the most important variable?
Recent payment history (last month) captures the client's current behavior. If they paid late this month, they are more likely to remain risky.

### 2. Why does AGE have low correlation with default?
Age does not determine payment capacity. A young client may have a better history than an older one.

### 3. Why segment clients?
Not all clients are equal. A client with good history deserves a different threshold than one with poor history.

### 4. Why use Conformal Prediction?
Uncertainty is critical in risk. It's not enough to predict; we need to know when to trust the prediction.

### 5. What is the main trade-off?
**Recall vs Precision:**
- High recall → capture more defaults → more false alarms (FP)
- High precision → fewer false alarms → miss some defaults (FN)

---

## 📊 Key Results

### Base Model (Threshold 0.10)
- Accuracy: 39.72%
- Recall: 93.07%
- Precision: 25.95%

### Segmented Model
- Accuracy: 72.87%
- Recall: 62.25%
- Precision: 42.29%
- Estimated Total Cost: $350,000

### Economic Capital
- Estimated reserve: $252,000
- Net profitability: $1,938,000 (with $500/client revenue)

---

---

## 💡 Technical Decisions

| Decision | Rationale |
|----------|-----------|
| **XGBoost** | Best performance on tabular data with class imbalance |
| **SMOTE 0.8** | Balances without over-synthesizing minority class |
| **Scale_pos_weight 4.0** | Compensates for class imbalance |
| **Manual Conformal Prediction** | MAPIE 1.4.1 API incompatibility |
| **Segmentation by risk** | Different clients need different thresholds |
| **Threshold optimization by cost** | Aligns model with business reality |

---

## 🔍 Model Interpretability

### Feature Importance (Top 5)

| Feature | Importance | Interpretation |
|---------|------------|----------------|
| `PAY_0` | High | Current payment status is the best predictor |
| `PAY_2` | High | Previous month payment status |
| `PAY_3` | Medium-High | Payment pattern over time |
| `LIMIT_BAL` | Medium | Credit limit as proxy for solvency |
| `AGE` | Low | Age has minimal predictive power |

### Why These Features Matter

| Feature | Why It Matters |
|---------|----------------|
| **Payment history** | Captures the client's financial behavior over time |
| **Credit limit** | Proxies income and capacity to pay |
| **Age** | Low impact → not a reliable risk indicator |

---

## 📈 Business Impact

### Scenario Analysis

| Scenario | Clients Approved | Revenue | Capital Reserve | Net Profit |
|----------|------------------|---------|-----------------|------------|
| **Optimistic** | 4,380 | $2,628,000 | $176,400 | $2,451,600 |
| **Realistic** | 4,380 | $2,190,000 | $252,000 | $1,938,000 |
| **Pessimistic** | 4,380 | $1,752,000 | $327,600 | $1,424,400 |

**Conclusion:** The model is profitable even in pessimistic scenarios.

---

## 💭 Personal Reflections

### What I Learned

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

1. **Start with costs, not accuracy**
   - Define business costs before building the model
   - This would guide all decisions from the beginning

2. **Explore more features**
   - Interaction variables between payment and billing
   - Trend indicators (improving or worsening behavior)

3. **Try ensemble methods earlier**
   - XGBoost + LightGBM + RandomForest
   - Would have improved performance faster

4. **Validate with out-of-time data**
   - Test model stability over time
   - Ensure it generalizes beyond the training period

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

## 📅 Revision History

| Date | Changes |
|------|---------|
| July 2026 | Initial version - English translation |
| July 2026 | Added Economic Capital analysis |
| July 2026 | Added segmentation results |
| July 2026 | Added lessons learned and hypotheses |

---

*📅 Last updated: July 2026*