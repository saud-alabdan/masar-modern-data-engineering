# Masar Mini-Lakehouse — Saud Alabdan

This project was developed as part of **Modern Data Engineering for AI Systems (SDA-DSC-214)** at [SDAIA Academy](https://github.com/SDAIAAcademy). `#SDAIAAcademy`

أُنجز هذا المشروع ضمن **برنامج هندسة البيانات الحديثة لأنظمة الذكاء الاصطناعي (SDA-DSC-214)** لدى [أكاديمية سدايا](https://github.com/SDAIAAcademy). `#SDAIAAcademy`

A working Bronze → Silver → Gold pipeline with real Delta Lake and PySpark. It turns three messy feeds from a fictional ride-hailing operator (trips CSV, drivers CSV, GPS events NDJSON) into trusted BI tables and leak-free AI feature tables. **All data is synthetic.** The eight course labs are the project.
خط بيانات فعلي من Bronze إلى Silver ثم Gold باستخدام Delta Lake وPySpark، يحوّل بيانات مشغل رحلات افتراضي إلى جداول تقارير موثوقة وجداول خصائص للذكاء الاصطناعي. **جميع البيانات اصطناعية.** اللابات الثمانية هي المشروع.

**Credits.** Course code, dataset, guides and templates are by Meaad Al-Marri ([course repository](https://github.com/almiyead-rgb/masar-modern-data-engineering)); this fork keeps them with attribution. The course home page is in [COURSE_README.md](COURSE_README.md). The executed notebooks, reports, lab notes and decision records are my own run and writing.

## Architecture

| Layer | What it guarantees in my run |
|---|---|
| Bronze | Append-only Delta tables with source file, SHA-256, batch id and ingestion time; the replay kept 144 deliveries for 72 trips |
| Silver | One typed row per `trip_id` (UTC times, local date, normalized cities, validated drivers), deterministic revision precedence, late trips added without duplicates; the same graph also runs in dbt |
| Delta safety | Constraint and schema enforcement, correction via MERGE, time travel, RESTORE and VACUUM dry run on sandbox copies only |
| Streaming | Kafka producer → Spark Structured Streaming → Delta with one persistent checkpoint; stop/restart and replays recorded |
| Quality gate | Great Expectations + policy checks; invalid rows quarantined with reasons; failed candidates never promoted |
| Gold / BI / AI | Eight tables published as a release behind a pointer; BI star schema and point-in-time features with unobserved labels left null |

Data contract and grains: [day02/DATA_CONTRACT.md](day02/DATA_CONTRACT.md) · [day05/DATA_PRODUCTS.md](day05/DATA_PRODUCTS.md) · [data/DICTIONARY.md](data/DICTIONARY.md). Key choices are in [DECISIONS.md](DECISIONS.md): revision precedence before MERGE, aggregating events before the join, and pointer-based releases.

## Deliverables

| Requirement | Where |
|---|---|
| Five executed daily notebooks (outputs retained) | [day01/STUDENT.ipynb](day01/STUDENT.ipynb) · [day02/STUDENT.ipynb](day02/STUDENT.ipynb) · [day03/STUDENT.ipynb](day03/STUDENT.ipynb) · [day04/STUDENT.ipynb](day04/STUDENT.ipynb) · [day05/STUDENT.ipynb](day05/STUDENT.ipynb) |
| Lab notes | [LAB01](LAB01_NOTES.md) · [LAB02](LAB02_NOTES.md) · [LAB03](LAB03_NOTES.md) · [LAB04](LAB04_NOTES.md) · [LAB05](LAB05_NOTES.md) · [LAB06](LAB06_NOTES.md) · [LAB07](LAB07_NOTES.md) · [LAB08](LAB08_NOTES.md) |
| Decisions, governance, benchmarks | [DECISIONS.md](DECISIONS.md) · [GOVERNANCE.md](GOVERNANCE.md) · [BENCHMARKS.md](BENCHMARKS.md) |
| Machine-generated run reports | [reports/](reports/) — bronze, benchmark and plans, Day 2–3 reports, dbt report and docs, streaming, quality (GX results, quarantine policy), Gold release and serving CSVs |
| Retained workspace archives | [Release `run-evidence`](https://github.com/saud-alabdan/masar-modern-data-engineering/releases/tag/run-evidence): `day01_handoff.zip` … `day05_handoff.zip` (Delta tables are kept out of Git) |
| Submission metadata | [submission.json](submission.json) |

My earlier Day 1 run from 13 Sep is kept as [day01/masar_modern_data_engineering_Saud_Alabdan.ipynb](day01/masar_modern_data_engineering_Saud_Alabdan.ipynb). The reports and notes above come from the complete five-day run on 15 Sep.

## Results

All five day sections ran in order in one kernel: 35 of 35 code cells, no errors, and 76 of 76 checks true across the 10 stage reports.
شُغّلت الأيام الخمسة بالترتيب: 35 خلية كود دون أخطاء، ونجحت الفحوص الـ76 كلها.

| Lab | Key result | Checks | Evidence |
|---|---|---|---|
| 01 Bronze | 144 trip deliveries = 72 trips; 6 drivers; 216 events | 7 / 7 | [bronze.json](reports/bronze.json) |
| 02 Scan | Same answer (SAR 1,794.60); CSV median 0.149 s vs Delta 0.768 s | 4 / 4 | [benchmark.json](reports/benchmark.json) |
| 03 Silver + dbt | 75 unique trips, SAR 1,875.60; dbt `PASSED_DBT_NATIVE` 72/72/75/75 | 5 + 5 | [day02_silver.json](reports/day02_silver.json), [dbt_attempt.json](reports/dbt/dbt_attempt.json) |
| 04 Delta | SYN_T0001 18.00 → 23.00; constraint and schema rejections; RESTORE as a new commit | 6 + 7 | [day03_transactions.json](reports/day03_transactions.json) |
| 05 Streaming | Transport 216/216/218/219 vs distinct events 216/216/216/217 | 13 / 13 | [day04_stream_latest.json](reports/day04_stream_latest.json) |
| 06 Quality | 82-row candidate failed, 7 quarantined (7 reasons); rechecked 75 approved | 9 / 9 | [day04_quality_latest.json](reports/day04_quality_latest.json) |
| 07 Gold | Injected failure after table 1; previous release stayed selected; rebuild identical | 4 / 4 | [day05_recovery.json](reports/day05_recovery.json) |
| 08 Serving | 16 serving checks; 3 features; 3 labels `UNOBSERVED` | 16 / 16 | [day05_serving_latest.json](reports/day05_serving_latest.json) |

**Reconciliation · المطابقة**

| Measure | Trusted Silver (after correction) | BI `fact_trips` | BI city summary | Difference |
|---|---:|---:|---:|---:|
| Trips | 75 | 75 | 25 + 25 + 25 = 75 | 0 |
| Fares (SAR) | 1,880.60 | 1,880.60 | 670.40 + 625.20 + 585.00 = 1,880.60 | 0.00 |
| Distinct GPS events | 217 (Day 4 snapshot) | 217 (sum of `gps_event_count`) | — | 0 |

## Environment I verified on

Windows 11 Pro, Intel i7-9700T (8 cores), 16 GB RAM, CPU only · Python 3.11.9 · Temurin Java 17.0.20.1 · PySpark 3.5.8 · delta-spark 3.3.3 · dbt-core 1.9.8 + dbt-spark[session] 1.9.1 · great-expectations 1.7.0 · pandas 2.2.3 · kafka-python 2.2.15 · Apache Kafka 4.0.2 (single local KRaft node).

Docker and WSL were not available, so I ran natively on Windows. That needed the following, none of which change a lab check or rule (see [DECISIONS.md](DECISIONS.md), item 10):

- `JAVA_TOOL_OPTIONS=-Duser.language=en -Duser.country=US -Duser.region=US -Dfile.encoding=UTF-8`. With the Arabic Windows locale, Delta wrote log files with Arabic-Indic digits and could not find its own commits.
- `HADOOP_HOME` with `bin/winutils.exe` and `bin/hadoop.dll`.
- `KAFKA_HEAP_OPTS=-Xms256m -Xmx512m`, so `kafka-server-start.bat` skips the removed `wmic` command.
- Three small patches in `src/masar`: forward-slash SQL paths (`dbt_lab.py`, `delta_lab.py`), parsing `file:/C:/…` locations (`dbt_lab.py`), and loading the Kafka connector when the first Spark JVM starts (`runtime.py`).

## How to run

```bat
:: from a clean clone of this branch, Python 3.11 and Java 17 installed
git clone -b develop https://github.com/saud-alabdan/masar-modern-data-engineering.git
cd masar-modern-data-engineering
py -3.11 -m venv .venv
.venv\Scripts\activate
python -m pip install -r requirements-course.txt
python -m pip check

:: Windows only (not needed on Linux/macOS)
set JAVA_TOOL_OPTIONS=-Duser.language=en -Duser.country=US -Duser.region=US -Dfile.encoding=UTF-8
set HADOOP_HOME=C:\path\to\hadoop
set PATH=%HADOOP_HOME%\bin;%PATH%

:: Kafka is needed before Day 4 (Docker route from the course)
docker compose -f infrastructure/kafka/compose.yaml up -d --wait

python -m jupyterlab
```

Run order: open `day01/STUDENT.ipynb` → `day05/STUDENT.ipynb` in order, Run All in each, and save with outputs. Each day continues from the successful Day 1 workspace in `outputs/day01_bronze_success.json` and ends by writing `outputs/dayNN_handoff.zip`. Expected results are the numbers in the tables above. Full setup and troubleshooting: [docs/SETUP.md](docs/SETUP.md), [day04/SETUP.md](day04/SETUP.md). Generated `outputs/` stay out of Git.

Using the outputs: the BI tables and AI features are exported as CSV in [reports/serving/](reports/serving/). In the label CSV, an empty field means "not observed", not zero.

## Limitations

- 72 synthetic base trips on one laptop; the timings are observations, not performance benchmarks, and say nothing about cloud cost or scale.
- Kafka ran as a single local broker; no failover, crash-recovery stress or watermark behaviour was tested.
- Authentication, TLS and row-level access are documented as proposals only (see [GOVERNANCE.md](GOVERNANCE.md)).
- No model was trained and no accuracy is reported; future labels stay null.

## Sources

Course references by Meaad Al-Marri: [SOURCES.md](SOURCES.md) and each day's `SOURCES.md`.
