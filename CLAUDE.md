# Fault Dataset Exploring

## Project Goal
Explore and visualize the Tennessee Eastman Process (TEP) fault detection dataset.

## Project Diagram

```
../datasets/TEP/Harvard/
└── dataverse_files.zip
    ├── TEP_FaultFree_Training.RData   ~25 MB   (normal operation, training)
    ├── TEP_FaultFree_Testing.RData    ~47 MB   (normal operation, testing)
    ├── TEP_Faulty_Training.RData      ~494 MB  (21 fault types, training)
    └── TEP_Faulty_Testing.RData       ~837 MB  (21 fault types, testing)

                        │
                        ▼ (pyreadr / rpy2)

┌──────────────────────────────────────────────────────────┐
│                    main.ipynb                            │
│                                                          │
│  1. Load Data          read RData → pandas DataFrames   │
│         │                                                │
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
  | File | Faults | Samples/fault | Duration |
  |------|--------|---------------|----------|
  | FaultFree_Training | 0 (normal) | 500 obs | 25h |
  | FaultFree_Testing  | 0 (normal) | 960 obs | 48h |
  | Faulty_Training    | 21         | 480 obs | 24h |
  | Faulty_Testing     | 21         | 960 obs | 48h |

## Environment

- Python 3 in Jupyter notebook (`main.ipynb`)
- Key libraries: `pyreadr`, `pandas`, `numpy`, `matplotlib`, `seaborn`
- Data path: `../datasets/TEP/Harvard/dataverse_files.zip`

## Variable Groups

| Index     | Description                        |
|-----------|------------------------------------|
| xmeas_1–22| Measured process variables         |
| xmeas_23–41| Analyzer measurements (sampled)  |
| xmv_1–11  | Manipulated variables (actuators)  |

## Confirmed DataFrame Schema (55 columns)

| Column(s) | Type | Description |
|-----------|------|-------------|
| `faultNumber` | float64 | 0 = fault-free, 1–21 = fault type |
| `simulationRun` | float64 | Independent simulation run ID |
| `sample` | int32 | Time step within a run (1-indexed) |
| `xmeas_1`–`xmeas_41` | float64 | Process measurements (41 variables) |
| `xmv_1`–`xmv_11` | float64 | Manipulated variables / actuators (11 variables) |

FaultFree_Training: 250,000 rows (500 runs × 500 samples)

## Notes
- RData files are loaded directly from the zip using `pyreadr` — no extraction needed.
- R object name inside the file: `fault_free_training` / check others similarly.
- Faults are injected at sample ~160 (8h mark at 3-min sampling).
- `pyreadr.read_r(io.BytesIO(data))` pattern used to read from zip in memory.
