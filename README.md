# Weekly Radar — Anomaly Detection on Weekly Network Performance

This project analyzes weekly mobile network performance data across Egyptian governorates to detect abnormal carrier and regional behavior. The dataset contains weekly KPI measurements for three operators, three technologies — LTE, 5G, and Multi-RAT — and 13 regions, with each row supported by a `sample_count` reliability indicator.

The main challenge is that the dataset contains only three weekly snapshots: 2026-03-01, 2026-03-08, and 2026-03-15. Because of this limited time history, the project avoids long-window time-series methods and instead focuses on cross-sectional comparison, week-over-week change analysis, technology comparison, and regional ranking.

## Project Objectives

The project answers five main analytical questions:

1. Which carriers are consistently below the regional average for download speed across all three weeks?
2. Which regions show the largest week-over-week change in download speed or latency?
3. Which region-carrier pairs are outliers compared with other carriers in the same region?
4. Is the 5G performance distribution different from LTE across regions?
5. How do the 13 regions rank from best to worst based on median download speed, and which regions have unstable rankings?

## Methodology

The workflow is divided into two main notebooks.

### 1. Preprocessing Notebook

The preprocessing phase cleans and prepares the raw workbook for analysis. The original dataset contained both daily and weekly records, locality-level rows, and country-level aggregate rows. The preprocessing notebook performs the following steps:

* keeps weekly records only;
* separates locality-level rows from country-level aggregate rows;
* aggregates locality rows into one clean `region × carrier × technology × week` table;
* uses `sample_count` as a weighting factor for KPI aggregation;
* applies a minimum `sample_count_total >= 30` threshold to remove weak observations;
* creates helper columns for mean-vs-median comparison;
* exports cleaned tables for the analysis phase.

### 2. Analysis Notebook

The analysis phase applies several explainable anomaly detection methods:

* **Cross-sectional peer comparison:** compares each carrier against other carriers in the same region, week, and technology.
* **Week-over-week change analysis:** detects regional download drops and latency increases between consecutive weeks.
* **Technology comparison:** compares LTE, 5G, and Multi-RAT download distributions.
* **Region ranking:** ranks all regions by sample-count-weighted median download speed.
* **Mean vs median analysis:** identifies skewed KPI distributions where the mean and median diverge strongly.

The main peer anomaly score is calculated as:

```text
Cross-sectional Badness =
max(0, -Download Gap (%)) + max(0, Latency Gap (%))
```

This score increases when a carrier has lower download speed and/or higher latency than its same-region peers.

## Key Results

* The cleaned analysis table contains 343 valid region-carrier-technology-week observations after applying the sample-count threshold.
* Three weekly snapshots, 13 regions, three carriers, and three technologies are preserved.
* 330 rows were suitable for complete 3-carrier peer comparison.
* Operator C was the only carrier consistently below the regional peer average across all three weeks.
* The largest download deterioration was observed in Assiut.
* The strongest combined week-over-week anomaly score was observed in Dakahlia because it combined both download deterioration and latency increase.
* Operator C on LTE dominated the strongest cross-sectional peer anomalies.
* 5G clearly outperformed LTE across all valid region-week comparisons.
* Port Said, Gharbia, and Assiut were among the strongest regions by median download ranking.
* Sohag remained consistently last, while Port Said showed the most noticeable rank movement.

## Deliverables

The project includes :

* preprocessing notebook
* analysis notebook
* cleaned CSV outputs
* peer anomaly log
* week-over-week anomaly log
* combined anomaly log
* carrier report
* region ranking table
* mean-vs-median analysis
* final PDF report
* 10-slide project presentation

## Main Lesson

With only three weekly data points, traditional long-history time-series anomaly detection is not the best fit. Cross-sectional comparison is more reliable in this case because it compares carriers and regions against direct peers at the same point in time. This makes the results easier to interpret and more suitable for operational network analysis. 
