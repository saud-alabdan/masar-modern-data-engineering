# Lab 01 Notes — Land and Inspect Raw Feeds

## Objective

Land the three fixed raw feeds into append-only Delta Bronze tables while preserving source provenance and ingestion evidence.

## Inputs

- `trips.csv`
- `drivers.csv`
- `gps.ndjson`

## Source Inspection

The fixed source manifest was verified before processing.

Observed source counts:

| Feed | Rows |
|---|---:|
| trips | 72 |
| drivers | 6 |
| gps_events | 216 |

The source inspection confirmed:

- Base keys are unique in the original source feeds.
- Required top-level fields are complete.
- Trip-to-driver and GPS-to-trip relationships are valid.
- Each trip has three GPS events.
- Source city labels match the expected base city set.

## Bronze Implementation

The three feeds were written as real Delta Bronze tables.

Bronze preserves the incoming source representation and records source and ingestion metadata for traceability.

The trips feed was ingested twice using separate batch IDs.

## Replay Evidence

The first trips delivery contained 72 rows.

The replay appended another 72 rows without overwriting the previous delivery.

Final Bronze trips count:

- Total deliveries: **144 rows**
- Distinct business trips: **72**

This demonstrates the intended append-only Bronze growth: replaying the same source delivery creates additional Bronze records while preserving the original delivery history.

## Quality Risks

### 1. Replay Duplication Risk — Observed

The trips Bronze table contains 144 delivered rows representing 72 distinct business trips after the intentional replay.

This is an expected consequence of append-only ingestion and must be handled by downstream deduplication logic.

### 2. Source Schema / Type Risk — Potential

Raw CSV values arrive as strings, including fields such as `fare_sar`, `distance_km`, and timestamps.

Downstream Silver processing must apply explicit typing and validation before analytical use.

### 3. City Label Consistency Risk — Potential

City labels are source-system values and may require normalization when multiple source systems or spelling conventions are introduced.

Normalization belongs in downstream processing while Bronze preserves the original source representation.

## Evidence

- Source inspection report: `source_inspection.json`
- Bronze verification report: `outputs/day01_bronze_g_589daf/reports/bronze.json`
- Bronze Delta evidence: `outputs/day01_bronze_g_589daf/`- Executed combined notebook: `masar_modern_data_engineering_Saud_Alabdan.ipynb`

## Result

Lab 01 Bronze ingestion and replay checks completed successfully.

The Bronze layer preserves source history, provenance, and replay evidence for downstream processing.