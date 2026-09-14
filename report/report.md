---
title: "Feature Screening in Breast Cancer Diagnosis: A Visual Exploration of the Wisconsin Diagnostic Dataset"
subtitle: "DSM050 – Data Visualisation, Midterm Coursework"
---

_[Word count: 1,978 words]_

## 1. Research Context and Problem Definition

Breast cancer is the most commonly diagnosed cancer among women worldwide. The World Health
Organisation estimates 2.4 million new diagnoses and 694,000 deaths globally in 2024, and it is
the leading cancer in women in 164 of 186 countries surveyed (World Health Organisation, 2024).
For decades, cytological assessment of fine-needle aspirate (FNA) biopsies, examining the size,
shape, and texture of cell nuclei under a microscope, has been a standard low-cost diagnostic
tool. Digitising this process opened the door to quantitative, computational approaches. Street,
Wolberg and Mangasarian (1993) showed that nuclear characteristics extracted from digitised FNA
images (radius, texture, perimeter, concavity, and related measures) could be computed
automatically and used to classify tumours as benign or malignant, producing the Breast Cancer
Wisconsin (Diagnostic) dataset used in this report. It remains a benchmark for
machine-learning-based diagnosis research, including recent classification studies (Yedjou et
al., 2021).

Rather than building a predictive classifier, this report focuses on **exploratory data analysis**
using visual techniques to understand which of the 30 measured tumour characteristics carry
diagnostic signal, and how that signal is structured across related groups of features.

**Research objective:** to identify, through univariate and multivariate visual analysis, which
tumour measurements most reliably distinguish malignant from benign diagnoses, and whether the
dataset's "worst" (most extreme) measurements carry more diagnostic signal than its "mean"
measurements.

**Target audience:** clinical and bioinformatics researchers evaluating which FNA-derived
measurements warrant closer attention when screening tumour samples.Findings are exploratory and descriptive, not diagnostic or
predictive, and are not intended to inform individual patient care.

## 2. Research Questions

**RQ1: Which tumour characteristics most strongly separate malignant from benign cases?**
With 30 candidate features, an analyst cannot inspect every measurement with equal attention.
Prior work using this dataset suggests a small subset of features, particularly those
describing nuclear shape irregularity (concavity, concave points) and size (radius, perimeter,
area), carry most of the classification signal (Yedjou et al., 2021). The first research question tests this directly
and visually, using effect size to rank all 30 features by how well they separate the two
diagnostic classes, rather than relying on classification accuracy alone, which can obscure
which variables are doing the discriminating.

**RQ2: Do the "worst" (largest/most extreme) measurements discriminate malignancy better than
the "mean" measurements?**
The dataset's structure, each of 10 base characteristics recorded as a mean, a standard error,
and a "worst" value (Street, Wolberg & Mangasarian, 1993), is a design decision worth
investigating. If malignancy is associated with irregular, heterogeneous cell populations, the
most extreme cells in a sample may be more diagnostically informative than the sample average.
The second research question evaluates this using correlation strength, directly comparing each mean/worst feature pair,
with practical implications for which measurements should be prioritised in feature engineering
or manual review.

Both questions are feasible within the dataset's scope (569 labelled samples, complete records,
established provenance) and are addressed using univariate and multivariate visualisation
rather than predictive modelling.

## 3. Dataset and Preprocessing

**Dataset.** The Breast Cancer Wisconsin (Diagnostic) dataset, accessed via
`sklearn.datasets.load_breast_cancer`, contains 569 tumour samples, each described by 30 numeric
features and a binary diagnosis (357 benign, 212 malignant, a 63%/37% split). The 30 features
are 10 base nuclear characteristics (radius, texture, perimeter, area, smoothness, compactness,
concavity, concave points, symmetry, fractal dimension), each expressed three ways: `mean`,
`standard error`, and `worst` (the largest/most extreme value observed across cells in the
sample) (Street, Wolberg & Mangasarian, 1993). The exact CSV used is included in `data/`.

**Handling missing values.** An explicit check (`df.isnull().sum()`) confirmed zero missing
values and zero duplicate rows across all 569 records. No
imputation, row removal, or placeholder substitution was therefore required.

**Managing dimensionality.** Thirty numeric features are too many to visualise meaningfully at
once. Two preprocessing decisions were used to manage this issue:

1. Features were grouped by measurement type (`mean_*`, `*_error`, `worst_*`), so visualisations
   compare feature families rather than 30 unrelated columns. This grouping is what makes RQ2
   answerable.
2. A **Cohen's d** effect size (Cohen, 1988) was computed for every feature against diagnosis,
   giving one comparable ranking of discriminative power.

**Scaling.** A z-score standardisation (`StandardScaler`) was fitted across all 30 features,
because the raw features each have very different scales, `mean area` reaches into the thousands
of square pixels, while `mean smoothness` is a fraction between 0 and 0.2, so any comparison of
magnitude or spread across features would therefore be dominated by whichever variable has the
largest raw units.

## 4. Exploratory Data Analysis (EDA)

### 4.1 Univariate analysis (RQ1)

**Class balance.** The diagnosis variable (nominal, two levels) splits 357 benign to 212
malignant (Figure 1), since the benign group both establishes the "typical" distribution and contains
more samples.

![Class balance between benign and malignant diagnoses.](../figures/u1_class_balance.png)

**Distribution shape.** Before comparing features by class, six representative `mean_*`
features were plotted as simple histograms across all 569 tumours (Figure 2), to check overall
shape and skew ahead of any group-based comparison.

![Distribution shape of six core tumour measurements, all tumours combined.](../figures/u2_feature_histograms.png)

**Ranked discriminative power.** Every one of the 30 features was scored with Cohen's d against
the diagnosis label; Figure 3 shows the top 12, ranked by magnitude.

![Top 12 features ranked by standardised mean difference (Cohen's d) between malignant and benign tumours.](../figures/u3_ranked_effect_sizes.png)

**Distribution by diagnosis.** Figure 4 returns to the six top-ranked features from Figure 3,
now split by diagnosis, to show the actual separation in each distribution that the effect-size
ranking is summarising numerically.

![Boxplot distributions of the six most discriminating features, split by diagnosis.](../figures/u4_boxplots_top_features.png)

### 4.2 Multivariate analysis (RQ2)

**Mean vs. worst, feature by feature.** Figure 5 compares, for each of the 10 base
characteristics, the absolute correlation with diagnosis for its `mean_*` version against its
`worst_*` version.

![Grouped comparison of mean_* vs worst_* |correlation| with diagnosis, for the four base characteristics with the strongest signal.](../figures/m1_mean_vs_worst_correlation.png)

**Relationship between mean and worst measurements.** Figure 6 plots mean radius against worst
radius directly, coloured by diagnosis, to show how the two versions of the same measurement
relate to one another and to class.

![Mean vs. worst radius, coloured by diagnosis.](../figures/m2_scatter_mean_vs_worst_radius.png)

**Internal correlation structure.** Figure 7 places the correlation matrix of the `mean_*`
feature block alongside that of the `worst_*` feature block, to check whether the two families
of features are structured similarly or differently among themselves.

![Correlation structure within the mean_* feature block (left) and the worst_* feature block (right).](../figures/m3_correlation_heatmaps.png)

**Two-dimensional class separation.** Finally, Figure 8 plots the two most discriminating
`worst_*` features (identified in Figure 3) against each other, to see how well class separation
holds when two strong features are considered jointly rather than one at a time.

![Class separation using the two strongest worst_* features (numeric × numeric × categorical).](../figures/m4_scatter_top_worst_features.png)

### 4.3 Dashboard

Figure 9 consolidates the findings above into a single, static, at-a-glance view designed for a
clinical/bioinformatics research audience who need a summary rather than the full sequence of
exploratory charts. For example, a researcher deciding which measurements to prioritise when
manually reviewing a batch of borderline samples. The layout follows a deliberate information
hierarchy (stated directly on the figure): dataset scope, class balance, which features matter
most, the mean-vs-worst framing from RQ2, and finally a visual demonstration of class separation
on the two strongest features. Colour is used consistently with every other figure in this
report (blue = benign, red = malignant; a fixed categorical assignment, never reused for
anything else), so the dashboard reads as part of the same visual system rather than a separate,
disconnected artefact.

![Feature-screening dashboard summarising class balance, top discriminating features, the mean-vs-worst comparison, and class separation on the strongest feature pair.](../figures/dashboard.png)

## 5. Interpretation and Discussion of Findings

### 5.1 RQ1: Which features separate malignant from benign tumours?

The top-ranked features by Cohen's d, worst_concave_points (d=2.60), worst_perimeter (2.37), mean_concave_points (2.33), worst_radius (2.32), mean_perimeter (2.12), and mean_radius (2.05), all share a clear physical theme, they describe nuclear size (radius, perimeter) and boundary irregularity (concave points, the count of inward dents in a nucleus's outline). This indicates that malignant nuclei in this dataset tend to be both larger and more irregularly shaped than benign nuclei, consistent with established cytological understanding of malignancy. By contrast, the lowest-ranked features, mean_fractal_dimension, texture_error, symmetry_error (all with |d| near 0), are either noise-sensitive error terms or a highly abstract complexity measure, and appear to carry little of the signal that actually distinguishes the two classes. The boxplots in Figure 4 support this ranking visually, the top features show a real shift in distribution between classes, though with some overlap rather than a clean distinction, meaning these measurements are strongly informative in the aggregate but not individually decisive for every case. For a clinician or researcher, this suggests size and shape-irregularity measurements warrant closer attention when screening samples, rather than symmetry or fractal-dimension-based metrics.

### 5.2 RQ2: Do "worst" measurements discriminate better than "mean" measurements?

The mean-vs-worst comparison (Figure 5) shows the pattern is not uniform across features. For most characteristics, concave points, perimeter, radius, area, the worst*\* version correlates more strongly with diagnosis than the mean*\* version, most dramatically for fractal_dimension, where the mean carries almost no signal (r=0.013) but the worst value shows a moderate relationship (r=0.324). This supports the idea that the single most extreme cell in a sample can reveal diagnostic signal that gets diluted when averaged across an entire sample. However, concavity and compactness break this pattern, with the mean correlating marginally more strongly than the worst value, suggesting that for these two characteristics, the overall shape of the cell population is more informative than any single outlier cell. Figures 6 and 7 support this, the mean and worst versions of radius are highly correlated with each other, and the internal correlation structure of the two feature blocks is broadly similar, indicating that "worst" measurements are not capturing an entirely different signal, but rather sharpening the same underlying signal for size/shape features specifically. This implies that dataset design should not default to reporting either mean or worst values alone, but consider which is more informative on a per-feature basis.

### 5.3 Implications, limitations, and audience relevance

For the stated audience of clinical and bioinformatics researchers, these findings suggest that a shortlist of nuclear size and irregularity measurements, rather than all 30 features, could reasonably guide manual review of borderline samples. However, several limitations are present in this conclusion. This analysis is descriptive and exploratory. Effect size and correlation quantify separation and linear association but do not establish predictive accuracy, causal mechanism, or clinical validity, which would require a proper classification model with held-out validation. The dataset itself is a single-institution, historical sample (Street, Wolberg & Mangasarian, 1993), and its class balance (63% benign, 37% malignant) may not reflect the true relationship in a screening population, meaning the "typical" distribution shown here is shaped by this particular sample rather than the wider population. Finally, both effect size and Pearson-style correlation summarise mean-level, broadly linear separation, and may understate the diagnostic value of features with more complex, non-linear relationships to malignancy. A natural follow-up would be predictive modelling with proper validation, to test whether the features identified here as most discriminative also perform best in an actual classifier.

## 6. Declaration of AI Tool Use

Generative AI (Claude, Anthropic) was used during this coursework as follows: **(1)** drafting
and debugging the Python/Matplotlib preprocessing and visualisation code in
`notebook/DSM050_breast_cancer_analysis.ipynb`, including resolving library version issues;
**(2)** applying a structured colour and layout methodology (consistent categorical colour
assignment, no dual-axis charts, accessible colour pairing) across the nine figures and
dashboard; **(3)** searching for and verifying the citations in Section 7; **(4)** drafting the
structural skeleton and prose for Sections 1–4.

AI was **not** used to generate, alter, or fabricate data, to select which statistical findings
to report, or to write Section 5 (Interpretation and Discussion). All AI-assisted code was executed and its output independently reviewed; all
AI-suggested citations were cross-checked against their original sources before use.

## 7. Reference List

Cohen, J. (1988). _Statistical Power Analysis for the Behavioral Sciences_ (2nd ed.). Lawrence
Erlbaum Associates.

Kelleher, C. and Wagener, T. (2011). Ten guidelines for effective data visualization in
scientific publications. _Environmental Modelling & Software_, 26(6), pp.822–827.
https://doi.org/10.1016/j.envsoft.2010.12.006

Qin, X., Luo, Y., Tang, N. et al. (2020). Making data visualization more efficient and effective:
a survey. _The VLDB Journal_, 29, pp.93–117. https://doi.org/10.1007/s00778-019-00588-3

Street, W.N., Wolberg, W.H. and Mangasarian, O.L. (1993). Nuclear feature extraction for breast
tumor diagnosis. In: _IS&T/SPIE 1993 International Symposium on Electronic Imaging: Science and
Technology_, vol. 1905, pp.861–870. https://doi.org/10.1117/12.148698

World Health Organization (2024). _Breast cancer_. [online] Available at:
https://www.who.int/news-room/fact-sheets/detail/breast-cancer [Accessed 13 Jul. 2026].

Yedjou, C.G., Tchounwou, S.S., Aló, R.A., Elhag, R., Mochona, B. and Latinwo, L. (2021).
Application of machine learning algorithms in breast cancer diagnosis and classification.
_International Journal of Scientific and Academic Research_, 2(1), pp.3081–3086.

GitHub Repo Link: https://github.com/jayminic/dsm050-midterm-breast-cancer-visualisation
