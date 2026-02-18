# Audit 02: Deconstructing Statistical Lies

**Course:** ECON 3916 | Module 2: Probability, Robustness & Sampling  
**Role:** Data Quality Auditor @ Pareto Ventures  
**Tools:** Python, NumPy, Pandas, Matplotlib, SciPy

---

## Overview
Three portfolio companies claimed "perfect" metrics. This audit used statistical 
forensics to find the lies hidden inside their averages.

---

## Finding 1: Latency Skew (NebulaCloud)
**The Claim:** Mean latency of 35ms.  
**The Lie:** 20 spike requests (1000ms–5000ms) out of 1,000 destroyed the mean.

| Metric | Value |
|--------|-------|
| Mean | ~115ms |
| Median | ~33ms |
| SD | ~350ms |
| MAD | ~15ms |

**Key Takeaway:** SD was exploded 20x by outliers while MAD stayed stable. 
The mean is a vanity metric in a Pareto World — tail latency is what kills users.

---

## Finding 2: False Positive Paradox (IntegrityAI)
**The Claim:** 98% accurate plagiarism detector.  
**The Lie:** In a rare-event environment, accuracy is meaningless.

| Scenario | Base Rate | P(Cheater \| Flagged) |
|----------|-----------|----------------------|
| Bootcamp | 50% | 98.0% |
| Econ Class | 5% | 72.1% |
| Honors Seminar | 0.1% | 4.7% |

**Key Takeaway:** In the Honors Seminar, 95% of flagged students are innocent. 
This is the Base Rate Fallacy — when cheating is rare, false positives 
overwhelm true positives no matter how good the detector is.

---

## Finding 3: Survivorship Bias (Memecoin Graveyard)
**The Claim:** Crypto markets generate massive returns.  
**The Lie:** 99% of tokens fail and get deleted from the data.

| Group | Mean Market Cap |
|-------|----------------|
| All 10,000 tokens | $6,960 |
| Top 1% Survivors | $358,556 |
| **Bias Multiplier** | **51.5x** |

**Key Takeaway:** Analyzing only "listed" or "successful" coins overstates 
returns by 51x. This is Survivorship Bias — the graveyard is invisible in 
the data but it's where the truth lives.

---

## The Auditor's Verdict
> "The mean is not a lie — it is an incomplete truth. 
> In a skewed world, always report the median, the tail, and the dead."
