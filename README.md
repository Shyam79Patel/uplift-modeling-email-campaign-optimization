# Kevin Hillstrom MineThatData — Email Analytics & Campaign Optimization

A complete customer analytics project built on Kevin Hillstrom's real-world email marketing experiment. The goal was not just to ask *"who will buy?"* — but to answer the harder and more valuable question: **"who bought because of the email?"**

---

## The Business Problem

A retail company sent email campaigns to 64,000 existing customers - some received a Men's clothing email, some a Women's email, and some received nothing at all. Two weeks later, the company tracked visits, conversions, and spend.

The challenge with this kind of data is that some customers would have purchased regardless of the email. Targeting them wastes budget. The real opportunity lies in finding the customers who **only act when nudged** - and ignoring everyone else.

This project tackles exactly that.

---

## Dataset

- **Source:** Kevin Hillstrom's MineThatData Email Analytics Challenge (2008)
- **Size:** 64,000 customers, 12 features
- **Type:** Randomized Controlled Trial (RCT) — perfectly balanced across 3 groups
- **Target variables:** `visit`, `conversion`, `spend`

---

##  What We Built

### 1. Exploratory Data Analysis
Explored the dataset structure, distributions, and campaign-level summaries. Key finding: only about 1 in 100 customers converted, and the majority spent nothing - a zero-inflated, low-conversion environment that reflects real retail conditions.

### 2. A/B Testing
Used Chi-Square testing to statistically validate whether the email campaigns actually drove more conversions - or whether the observed differences were just noise.

**Result:** Both campaigns outperformed the control group significantly. The Men's email drove roughly double the conversion uplift of the Women's email. These findings were not luck - p-values were effectively zero across all pairwise comparisons.

| Campaign | Conversion Uplift vs Control |
|---|---|
| Men's Email | +0.68% |
| Women's Email | +0.31% |

### 3. RFM Segmentation
Segmented all 64,000 customers into four behavioral groups using Recency and Monetary scores (Frequency data was unavailable in this snapshot dataset).

| Segment | Description |
|---|---|
| Champions | Recent buyers, high historical spend |
| Loyal | Recently active, modest spenders |
| At Risk | High past spend but going cold |
| Lost | Inactive and low value |

The At Risk segment stood out as the most urgent - these are valuable customers who are drifting away. The Men's email had the strongest reactivation effect on this group.

### 4. Uplift Modeling
The most important part of this project. Instead of predicting who *will* buy, uplift modeling identifies who will buy **specifically because of the email** - a fundamentally different and more valuable question.

**Method:** Two separate XGBoost classifiers - one trained on customers who received the Men's email, one on the control group. The difference in their predicted probabilities for each customer becomes the **uplift score**.

```
Uplift Score = P(convert | received email) - P(convert | no email)
```

**Customer segments by uplift score:**

| Segment | Count | What it means |
|---|---|---|
| Persuadable | 5,308 | Email genuinely moves them - target these |
| Sure Thing / Weak | 26,891 | Marginal effect - low priority |
| Sleeping Dog | 10,414 | Email actually hurts conversion - never email these |

The sleeping dog finding is particularly important: over 10,000 customers showed a **negative** response to email. Targeting them doesn't just waste money - it actively reduces their likelihood of converting.

---

##  Final Targeting Recommendation

Combining RFM segments with uplift scores gives a precise targeting matrix:

| Priority | Segment | Count | Why |
|---|---|---|---|
| #1 | At Risk + Persuadable | 1,041 | Highest uplift, high-value customers going cold |
| #2 | Champions + Persuadable | 2,557 | Strong uplift, best spending history |
| #3 | Loyal + Persuadable | 859 | Consistent responders |
| #4 | Lost + Persuadable | 851 | Cold but surprisingly responsive |
| Never email | Any Sleeping Dog | 10,414 | Negative uplift — leave them alone |

**Bottom line:** Instead of emailing all 42,000 customers, focus the entire budget on 5,308 persuadables. Same or better conversions. A fraction of the cost.

---

##  Tech Stack

- **Python** - pandas, numpy, scipy, scikit-learn, xgboost
- **Statistical Testing** - Chi-Square test, pairwise comparisons
- **Modeling** - XGBoost Classifier (two-model uplift approach)
- **Segmentation** - Quantile-based RFM scoring

---

## 💡 Key Learnings

- A statistically significant campaign result does not mean every customer should be targeted - uplift modeling reveals who actually needs the nudge
- Zero-inflated conversion data is a real-world condition, not a modeling failure - understanding *why* a model struggles is as valuable as the model itself
- The control group is not wasted data - it is the foundation that makes causal inference possible
- Sleeping dogs are a real phenomenon. Sending emails to the wrong customers can hurt conversion rates, not just waste budget

---
