# FedSpeak Analysis — NLP on FOMC Minutes

## Objective

This project applies natural language processing to 240 Federal Open Market Committee (FOMC) meeting minutes from 2000–2026 to quantify the evolution of U.S. monetary-policy communication and identify structural shifts in central-bank tone across the GFC and COVID regimes.

## Methodology

- **Corpus construction.** Loaded 240 FOMC meeting minutes (avg. ~7,100 words) from the HuggingFace `vtasca/fomc-statements-minutes` dataset, filtered to minutes only, and indexed chronologically from February 2000 to March 2026.
- **Text preprocessing.** Lowercased, stripped non-alphabetic characters, tokenized with NLTK, removed English stop words, dropped tokens shorter than three characters, and lemmatized with WordNet to normalize morphological variants.
- **TF-IDF vectorization.** Built a 240 × 2,000 document–term matrix using scikit-learn's `TfidfVectorizer` with `min_df=5`, `max_df=0.85`, and `ngram_range=(1, 2)` to capture FOMC-specific bigrams (e.g., *accommodative monetary*, *accommodative stance*) alongside unigrams. Final matrix sparsity: 51.9%.
- **Loughran-McDonald sentiment scoring.** Computed two domain-specific tone measures per document — net sentiment `(positive − negative) / total tokens` and uncertainty `(uncertainty tokens) / total tokens` — using the simplified LM financial dictionary, the standard for monetary and financial text analysis.
- **Time-series visualization.** Plotted raw and 12-month rolling-average series for both sentiment and uncertainty, annotated with four structural events: Lehman (Sep 2008), the Taper Tantrum (May 2013), COVID (Mar 2020), and the 2022 tightening cycle.
- **Unsupervised regime detection.** Reduced the TF-IDF matrix to 50 components via TruncatedSVD (79.9% explained variance) and applied K-Means clustering (`k=3`, `n_init=10`) to identify latent "language regimes" in FOMC communications. Validated cluster separation with silhouette score (0.175, typical for high-dimensional text).
- **Pre/post-COVID comparison.** Split the corpus at March 2020 (190 vs. 50 meetings) and tested differences in mean sentiment and uncertainty using two-sample t-tests, with paired histogram and boxplot diagnostics.

## Key Findings

- **The LM dictionary independently identifies landmark policy moments.** The most negative meeting in the entire 26-year corpus is December 16, 2008 — the FOMC meeting that cut the federal funds rate to the zero lower bound. The most positive is June 30, 2004 — the start of the post-dot-com tightening cycle. No date information is given to the algorithm; the tone signal alone surfaces these inflection points.
- **K-Means on TF-IDF vectors recovers the post-Lehman regime break.** With `k=3`, the cluster boundary between Cluster 0 (n=79, March 2000 – October 2008) and Cluster 1 (n=134, December 2008 – March 2026) lands *exactly* on the December 16, 2008 emergency meeting. The unsupervised model independently rediscovered the structural break that monetary economists treat as the inflection point of the modern Fed.
- **Post-COVID FOMC communication has become measurably more guarded but not more uncertain.** Mean net sentiment fell from +0.0081 pre-COVID to +0.0028 post-COVID — a 65% decline that is highly statistically significant (t = 3.92, p = 0.0001). Mean uncertainty was essentially unchanged (t = 0.97, p = 0.33). The asymmetry is economically meaningful: the post-2020 Committee has been responding to concrete shocks (pandemic, supply disruptions, the 2022–2024 tightening cycle) rather than navigating ambiguity, so the language shift is in *valence* rather than in *certainty*.

## Stack

`Python 3.11` · `scikit-learn` · `NLTK` · `pandas` · `numpy` · `matplotlib` · `scipy` · `HuggingFace datasets`

## Repository structure

```
econ-lab-23-fedspeak/
├── notebooks/
│   └── lab-ch23-fedspeak.ipynb
├── figures/
│   ├── sentiment_timeseries.png
│   ├── kmeans_clusters_pca.png
│   └── pre_post_covid_comparison.png
└── README.md
```
