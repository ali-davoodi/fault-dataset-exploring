# Tennessee Eastman Process — Fault Dataset Exploration

Exploratory analysis and visualization of the [TEP fault detection dataset](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/6C3JR1) from Harvard Dataverse.

## Dataset

The Tennessee Eastman Process (TEP) is a simulated chemical plant benchmark for fault detection research.

| File | Description | Rows |
|------|-------------|------|
| `ff_train.parquet` | Normal operation, training | 250,000 |
| `ff_test.parquet` | Normal operation, testing | 480,000 |
| `f_train.parquet` | 21 fault types, training | 5,000,000 |
| `f_test.parquet` | 21 fault types, testing | 9,600,000 |

- **52 process variables**: 41 measurements (`xmeas_1`–`xmeas_41`) + 11 manipulated variables (`xmv_1`–`xmv_11`)
- **21 fault types** (IDV 1–21), injected at simulation hour 8 (sample 160)
- **Sampling rate**: 1 observation every 3 minutes

## Notebook Contents

`main.ipynb` walks through:

1. **Load Data** — memory-efficient parquet loading: `ff_train` loaded fully (55 MB); `f_train` processed fault-by-fault (~30 MB peak); `f_test` filtered to needed faults + columns (~90 MB vs 2.1 GB full)
2. **Basic Exploration** — schema, dtypes, missing values, descriptive statistics, fault type distribution
3. **Time Series — Normal Operation** — all 52 variables plotted for a single fault-free run
4. **Fault vs. Normal Comparison** — 6 key variables across fault-free and IDV 1/4/5 (testing set, run 1)
5. **Feature Distributions** — box plots and violin plots, normal vs. faulty (post-injection window)
6. **Correlation Heatmap** — Pearson correlation matrix for all features under normal operation
7. **Per-Fault Deviation Heatmap** — mean absolute z-score per feature per fault type
8. **Average Fault Trajectory** — mean time series across all runs, normal vs. selected faults

## Setup

```bash
pip install pyarrow pandas numpy matplotlib seaborn
```

Place the parquet files in `../datasets/TEP/Harvard/` relative to this repo, then run `main.ipynb`.
