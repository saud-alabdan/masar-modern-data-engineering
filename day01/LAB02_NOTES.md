# Lab 02 Notes — Cost, Elasticity, and Scan Benchmark

## Objective

Compare always-on and scheduled compute operation, then measure actual Spark scans of the same result from CSV and Delta.

## Operating Assumptions

The operating-cost comparison uses hypothetical teaching units (TU), not currency.

The model assumes:

- Always-on compute remains available for the full operating period.
- Scheduled compute runs only during the assumed workload window.
- Storage cost is represented separately.
- The comparison is an instructional model rather than a production cloud bill.

## Always-On vs Scheduled

Always-on operation provides continuously available compute but continues to incur compute cost during idle periods.

Scheduled operation starts compute around the workload and releases it afterward. This can reduce compute overhead when workloads are intermittent.

A counterexample is a workload that runs frequently enough that startup and shutdown overhead becomes significant. In that situation, always-on or a longer-lived compute resource can be operationally preferable.

## Cost Model Result

The model produced the following illustrative result:

| Metric | Always-on | Scheduled |
|---|---:|---:|
| Compute | 1440.00 TU | 135.0000 TU |
| Storage | 20.00 TU | 20.00 TU |
| Total | 1460.00 TU | 185.0000 TU |

These values are hypothetical teaching units and should not be interpreted as actual currency prices.

The model also shows that the advantage of scheduled operation depends on workload duration. At approximately 23.25 scheduled hours, the modeled totals become equal.

## Scan Benchmark

The benchmark compared the same aggregate query against the trips data in CSV and Delta format.

The result was checked for equality before comparing timings.

Observed aggregate result:

- Rows: 72
- Non-null fares: 72
- Fare total: 1794.60

### CSV timings

- 0.082588167 s
- 0.081717423 s
- 0.107910647 s
- 0.112941477 s
- Median: 0.095249407 s

### Delta version 0 timings

- 0.582186812 s
- 0.551852447 s
- 1.012981715 s
- 0.847043724 s
- Median: 0.714615268 s

The observed timings show CSV completing faster for this small benchmark dataset. This result should not be generalized into a production performance claim.

Repeated measurements can vary because of JVM, operating-system, metadata, and cache effects. The benchmark therefore retains the individual measurements rather than reporting only a single timing.

## Query Plans

The actual query plans were retained for both scans:

outputs/day01_bronze_g_589daf/reports/plans/csv.txt
outputs/day01_bronze_g_589daf/reports/plans/delta_v0.txt

The benchmark includes both result equality checks and execution-plan evidence.

## Evidence

- Benchmark report: `outputs/day01_bronze_g_589daf/reports/benchmark.json`
- CSV query plan: `reports/plans/csv.txt`
- Delta query plan: `reports/plans/delta_v0.txt`
- Executed combined notebook: `masar_modern_data_engineering_Saud_Alabdan.ipynb`

## Result

Lab 02 benchmark and cost-model checks completed successfully.

The experiment demonstrates that compute economics depend on workload shape and that observed performance on a small dataset should be reported as an observation rather than treated as a universal technology comparison.