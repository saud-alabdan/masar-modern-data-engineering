# Lab 03 — ELT and Silver · التحويل وبناء Silver

**Result.** Staging kept 144 trip receipts, 6 drivers and 216 events. Silver ended with 75 unique trips (SAR 1,875.60) after the late batch, and dbt passed with 72 / 72 / 75 / 75 rows and the same digests as Spark.
**النتيجة.** احتفظت طبقة التهيئة بـ144 سجل وصول و6 سائقين و216 حدثًا، وانتهت Silver بـ75 رحلة فريدة (1,875.60 ريال) بعد الدفعة المتأخرة، ونجح dbt بالأعداد 72 / 72 / 75 / 75 وبنفس بصمات Spark.

**How it works.** Cities are trimmed and normalized (" riyadh " → Riyadh, 10 rows), times stored in UTC with the local date from Asia/Riyadh, and drivers checked unique before the join. In dbt, `source('bronze','trips')` feeds `stg_trips`, `ref()` chains it to `int_trip_candidates` (latest revision per trip) and `silver_trips`, which looks back 3 days on ingestion time.
**آلية العمل.** وُحّدت أسماء المدن (" riyadh " ← Riyadh في 10 صفوف)، وحُفظ الوقت بتوقيت UTC مع التاريخ المحلي للرياض، وتحققنا من تفرد السائقين قبل الربط. في dbt يغذي `source()` نموذج `stg_trips`، ويربطه `ref()` بنموذج المرشحين ثم `silver_trips` الذي ينظر 3 أيام للخلف حسب وقت الاستلام.

**Business key and precedence.** One row per `trip_id`. The winner is the highest `source_revision`, then the latest `_ingested_at`, then the smallest `_source_sha256` and `_batch_id`, so every rerun picks the same row. The same revision with a different payload fails the pre-merge test instead of being chosen.
**مفتاح الأعمال والأولوية.** صف واحد لكل `trip_id`؛ الفائز أعلى مراجعة، ثم أحدث وقت استلام، ثم أصغر بصمة ومعرف دفعة، فيختار كل تشغيل الصف نفسه. ونفس المراجعة بمحتوى مختلف تُفشل اختبار ما قبل الدمج بدل اختيار أحدها.

Evidence · الدليل: [reports/day02_staging_latest.json](reports/day02_staging_latest.json) · [reports/day02_silver.json](reports/day02_silver.json) · [reports/dbt/dbt_attempt.json](reports/dbt/dbt_attempt.json) · [dbt docs](reports/dbt/index.html) · [day02/STUDENT.ipynb](day02/STUDENT.ipynb) cells In [20]–In [22]

## Questions · الأسئلة

1. 144 receipts are the same 72 trips delivered twice, so Silver keeps one row per `trip_id`.
   الـ144 سجلًا هي نفس الرحلات الـ72 وصلت مرتين، لذلك تحتفظ Silver بصف واحد لكل رحلة.
2. A duplicate driver key makes each of that driver's trips match twice, inflating trip counts and fares.
   تكرار مفتاح السائق يجعل رحلاته تتطابق مرتين فتتضخم الأعداد والأجور.
3. Demand uses the trip date (June 1); freshness uses the arrival date (June 4).
   تقرير الطلب يستخدم تاريخ الرحلة (1 يونيو)، وفحص الحداثة يستخدم تاريخ الوصول (4 يونيو).
4. Same trip with a different fare is a change to the business record, so it needs revision rules — not silent deduplication.
   اختلاف الأجرة للرحلة نفسها تغيير في البيانات يحتاج قاعدة مراجعة، لا حذف تكرار صامت.
5. A log version counts commits, not content; a correct rerun can add a commit while the digest stays identical.
   رقم السجل يعدّ عمليات الحفظ لا المحتوى، فقد يضيف التشغيل الصحيح نسخة مع بقاء البصمة نفسها.
6. Each base trip has three GPS events, so a raw join repeats the fare three times; count events per trip first.
   لكل رحلة ثلاثة أحداث، فالربط المباشر يكرر الأجرة ثلاث مرات؛ الحل تجميع الأحداث لكل رحلة أولًا.
7. A late event whose date equals the last processed maximum is skipped by a strict `>` filter; a lookback on ingestion time catches it.
   الحدث المتأخر الذي يساوي تاريخه آخر تاريخ معالج يُفقد مع شرط `>`؛ النظر للخلف حسب وقت الاستلام يلتقطه.
8. Fare, distance, end time and duration are only known after the trip ends, so they cannot be features at trip start.
   الأجرة والمسافة ووقت الانتهاء والمدة لا تُعرف إلا بعد انتهاء الرحلة، فلا تصلح خصائص عند بدايتها.
