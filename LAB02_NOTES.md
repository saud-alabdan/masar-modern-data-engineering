# Lab 02 — Cost Model and Scan Benchmark · نموذج التكلفة وقياس القراءة

**Result.** In the cost model (teaching units, not money), always-on costs 1,460 TU and scheduled costs 185 TU a month, breaking even at 23.25 work hours/day. In the Spark benchmark on the same 72 rows (fare total SAR 1,794.60), CSV had a median of 0.149 s and Delta version 0 0.768 s. All 4 checks passed, including equal results and saved query plans.
**النتيجة.** في نموذج التكلفة (وحدات تعليمية لا أموال) يكلف التشغيل الدائم 1,460 وحدة والمجدول 185 وحدة شهريًا، ويتعادلان عند 23.25 ساعة عمل يوميًا. في قياس Spark على نفس 72 صفًا (إجمالي 1,794.60 ريالًا)، كان وسيط CSV ‏0.149 ثانية ووسيط Delta (النسخة 0) ‏0.768 ثانية. نجحت الفحوص الأربعة، ومنها تطابق النتائج وحفظ خطط الاستعلام.

**My reading.** Delta was slower here because it reads its transaction log and Parquet metadata first, which does not pay off on 72 rows. I would not claim CSV is faster in general.
**قراءتي.** كان Delta أبطأ هنا لأنه يقرأ سجل المعاملات وبيانات Parquet أولًا، وهذا لا يُفيد مع 72 صفًا فقط، ولا أعمم أن CSV أسرع دائمًا.

Evidence · الدليل: [reports/benchmark.json](reports/benchmark.json) · [reports/plans/](reports/plans/) · [reports/cost_model_result.json](reports/cost_model_result.json) · [day01/STUDENT.ipynb](day01/STUDENT.ipynb) cells In [11]–In [14], In [17] · Details: [BENCHMARKS.md](BENCHMARKS.md)
