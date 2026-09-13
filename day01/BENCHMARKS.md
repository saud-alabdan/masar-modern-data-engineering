# Day 01 Benchmarks — Lab 02

## Benchmark Objective

Measure actual Spark scan performance for the same aggregate query against the trips data in CSV and Delta format.

The benchmark also compares the modeled compute cost of always-on and scheduled operation.

## Cost Model

The operating-cost comparison uses hypothetical teaching units (TU), not currency.

| Metric | Always-on | Scheduled |
|---|---:|---:|
| Compute | 1440.00 TU | 135.0000 TU |
| Storage | 20.00 TU | 20.00 TU |
| Total | 1460.00 TU | 185.0000 TU |

The modeled totals become equal at approximately 23.25 scheduled hours.

This is an instructional cost model and does not represent actual cloud pricing.

## Scan Benchmark

The same aggregate query was executed against CSV and Delta.

Result equality was checked before comparing execution times.

| Metric | Result |
|---|---:|
| Rows | 72 |
| Non-null fares | 72 |
| Fare total | 1794.60 SAR |

### CSV

| Run | Time (s) |
|---|---:|
| 1 | 0.082588167 |
| 2 | 0.081717423 |
| 3 | 0.107910647 |
| 4 | 0.112941477 |
| Median | 0.095249407 |

### Delta Version 0

| Run | Time (s) |
|---|---:|
| 1 | 0.582186812 |
| 2 | 0.551852447 |
| 3 | 1.012981715 |
| 4 | 0.847043724 |
| Median | 0.714615268 |

## Interpretation

For this small benchmark dataset, CSV completed the measured query faster than Delta Version 0.

This is an observed result for this experiment and should not be generalized into a production performance claim.

Repeated measurements can vary because of JVM, operating-system, metadata, and cache effects.

## Query Plans

The actual query plans were retained:

- `outputs/day01_bronze_g_589daf/reports/plans/csv.txt`
- `outputs/day01_bronze_g_589daf/reports/plans/delta_v0.txt`

## Evidence

- Benchmark report: `outputs/day01_bronze_g_589daf/reports/benchmark.json`
- CSV query plan: `outputs/day01_bronze_g_589daf/reports/plans/csv.txt`
- Delta query plan: `outputs/day01_bronze_g_589daf/reports/plans/delta_v0.txt`
- Executed notebook: `masar_modern_data_engineering_Saud_Alabdan.ipynb`

## Conclusion

The benchmark demonstrates that compute economics depend on workload shape and that performance results on a small dataset should be reported as observed measurements rather than universal technology comparisons.