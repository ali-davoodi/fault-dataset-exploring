# Fault Dataset Exploring

## Project Goal
Explore and visualize the Tennessee Eastman Process (TEP) fault detection dataset.

## Project Diagram

```
../datasets/TEP/Harvard/
├── ff_train.parquet   ~21 MB   (normal operation, training)
├── ff_test.parquet    ~39 MB   (normal operation, testing)
├── f_train.parquet   ~459 MB  (21 fault types, training)
└── f_test.parquet    ~839 MB  (21 fault types, testing)

                        │
                        ▼ (pyarrow / pandas)

┌──────────────────────────────────────────────────────────┐
│                    main.ipynb                            │
│                                                          │
│  1. Load Data          parquet → pandas DataFrames      │
│         │              (column projection + row filters) │
│  2. Explore            shape, dtypes, stats, nulls,     │
│         │              variable descriptions             │
│         │                                                │
│  3. Visualize          time series, distributions,      │
│                        fault comparison, heatmaps        │
└──────────────────────────────────────────────────────────┘
```

## Dataset: Tennessee Eastman Process (TEP)

- **Source**: Harvard Dataverse
- **Type**: Simulated chemical plant process (benchmark for fault detection)
- **Process variables**: 52 total (41 measured + 11 manipulated)
- **Fault types**: 21 (IDV 1–21), injected at simulation time 8h
- **Sampling**: 1 observation every 3 minutes
- **Splits**:
  | File | Faults | Rows | Duration |
  |------|--------|------|----------|
  | ff_train.parquet | 0 (normal) | 250,000 | 25h |
  | ff_test.parquet  | 0 (normal) | 480,000 | 48h |
  | f_train.parquet  | 21         | 5,000,000 | 24h |
  | f_test.parquet   | 21         | 9,600,000 | 48h |

## Environment

- Python 3 in Jupyter notebook (`main.ipynb`)
- Key libraries: `pyarrow`, `pandas`, `numpy`, `matplotlib`, `seaborn`
- Data path: `../datasets/TEP/Harvard/`

## Variable Groups

| Index      | Description                       |
|------------|-----------------------------------|
| xmeas_1–22 | Measured process variables        |
| xmeas_23–41| Analyzer measurements (sampled)   |
| xmv_1–11   | Manipulated variables (actuators) |

## Confirmed DataFrame Schema (55 columns)

| Column(s) | Type | Description |
|-----------|------|-------------|
| `faultNumber` | float32 | 0 = fault-free, 1–21 = fault type |
| `simulationRun` | float32 | Independent simulation run ID |
| `sample` | int32 | Time step within a run (1-indexed) |
| `xmeas_1`–`xmeas_41` | float32 | Process measurements (41 variables) |
| `xmv_1`–`xmv_11` | float32 | Manipulated variables / actuators (11 variables) |

## Memory-Efficient Loading Strategy

Large parquet files are never loaded fully into memory. Instead:

| File | Strategy | Peak RAM |
|------|----------|----------|
| `ff_train.parquet` | Full load | ~55 MB |
| `f_train.parquet` | Fault-by-fault loop with column projection + row filter (`sample > 160`) | ~30 MB/iter |
| `ff_test.parquet` | Column projection (10 of 55 cols) | ~20 MB |
| `f_test.parquet` | Filter to 5 needed fault types + column projection | ~90 MB |

## Notes
- Faults are injected at sample 160 (8h mark at 3-min sampling).
- `pd.read_parquet(path, columns=[...], filters=[...])` used throughout — filters operate at row-group level via pyarrow predicate pushdown.
- All numeric columns are `float32` (downcast from `float64` during RData → parquet conversion).
