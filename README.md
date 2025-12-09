# Flow Duration Feature Analysis — CICIDS2019

## Overview

This repository analyzes the **Flow Duration** feature from the DDoS and BENIGN traffic dataset to investigate whether it can reliably distinguish between benign traffic and DDoS attacks.  
Using a combination of bootstrap sampling, log‑transformation, and permutation testing, we demonstrate that the average flow duration for attack traffic differs significantly from benign traffic — making it a potentially useful feature for detection or classification tasks.

### Why this matters

- Network traffic features (e.g., packet counts, durations, rates) are often **heavy‑tailed** and **right‑skewed**, which makes conventional statistics unstable.  
- The combination of the (CLT) + log‑transform + resampling enables robust estimation of sampling distributions.  
- A simple yet effective feature like Flow Duration — if validated — can support or simplify intrusion detection or traffic classification pipelines.  

---

## Hypotheses

- **Null Hypothesis (H₀):** There is no difference in average flow duration between benign and attack (DDoS) traffic.  
- **Alternative Hypothesis (H₁):** Attack traffic has a significantly higher average flow duration than benign traffic.  

The project tests whether observed differences are statistically significant — not likely to arise by chance alone.

---

## Dataset & Preprocessing

- **Dataset used:** CICIDS2019 — cleaned version (_e.g._, a filtered DataFrame named `clean_traffic`).  
- **Labels:** `BENIGN` and `DDoS`.  
- **Key feature analyzed:** `Flow Duration` (duration of each flow).  

### Preprocessing steps

- Drop missing values (`NaN`) and invalid durations (e.g. negative or placeholder values).  
- Replaced infinite values with the NaN values  
- Use log‑transformation before computing sample means to mitigate heavy skew.

---

## Methodology

1. **Bootstrap sampling (with replacement)**  
   - For both benign and attack flows, repeatedly draw large samples (e.g. n = 500 –1000), compute the sample mean of log‑transformed Flow Duration, and repeat many times (e.g. 1000 simulations).  
   - This produces sampling distributions of the mean for each group under the CLT assumption.

2. **Permutation test (label‑shuffling)**  
   - Shuffle the `Label` column (BENIGN / DDoS) across flows to break real grouping.  
   - Compute difference in group means (attack minus benign) for each shuffle.  
   - Build an empirical null distribution of mean differences.  
   - Compute a one‑sided p‑value: proportion of shuffled differences ≥ observed difference.

3. **Visualization & Interpretation**  
   - Histograms of bootstrapped sampling distributions (benign vs attack).  
   - Histogram of permutation distribution of mean differences with observed difference marked.  
   - Summary statistics: means, medians, standard errors, p‑value, percentage difference.  

---

## Results Summary (example)

- **Observed log‑mean difference:** ~ 0.10 → corresponds to ~10% higher average Flow Duration for DDoS flows compared to benign flows.  
- **Empirical p-value (one-sided):** ~ 0.20% (permutation test with many random shuffles) — extremely unlikely under the null.  
- **Conclusion:** Flow Duration is a statistically significant and stable feature for distinguishing benign vs DDoS traffic — worthy of inclusion in detection / classification models.  

> **Note:** While statistical significance is clear, practical performance (e.g. detection accuracy, false positives) should be further evaluated in a full modeling pipeline.

---

