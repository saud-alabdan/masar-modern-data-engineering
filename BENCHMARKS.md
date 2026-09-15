# Benchmarks · القياسات

Measured on my laptop only: Windows 11, Intel i7-9700T (8 cores), 16 GB RAM, Spark 3.5.8 `local[2]`, Delta 3.3.3. These numbers do not predict cloud cost or production speed.
قيست على جهازي فقط (Windows 11، معالج i7-9700T بثماني أنوية، ذاكرة 16 جيجابايت، Spark 3.5.8)، ولا تتنبأ بتكلفة السحابة أو سرعة الإنتاج.

## 1. CSV vs Delta scan (Lab 02) · قراءة CSV مقابل Delta

Same 72 rows and same answer (72 fares, SAR 1,794.60). There was 1 warm-up per format, then 4 timed runs in alternating order. Timing covers planning, the action and collect, but not session startup. No explicit Spark cache was used; OS/JVM caches were not controlled.
نفس 72 صفًا ونفس النتيجة، مع تهيئة واحدة لكل صيغة ثم 4 قياسات بترتيب متبادل. التوقيت يشمل التخطيط والتنفيذ والجمع دون إقلاع الجلسة، ودون ذاكرة Spark مؤقتة صريحة.

| Format | Run 1 (s) | Run 2 (s) | Run 3 (s) | Run 4 (s) | Median (s) |
|---|---:|---:|---:|---:|---:|
| CSV | 0.153 | 0.156 | 0.138 | 0.145 | **0.149** |
| Delta v0 | 0.789 | 0.747 | 0.741 | 0.885 | **0.768** |

Delta was about 5× slower on this tiny table because of log and metadata overhead. It was chosen for correctness and history, not speed.
كان Delta أبطأ بنحو 5 مرات على هذا الجدول الصغير بسبب قراءة السجل والبيانات الوصفية، واخترناه للموثوقية والتاريخ لا للسرعة.

## 2. Compaction (Lab 04) · ضغط الملفات

OPTIMIZE on a sandbox copy went from 4 files / 28,486 bytes to 1 file / 12,739 bytes with business values unchanged. No speed-up is claimed.
أمر OPTIMIZE على نسخة تجريبية حوّل 4 ملفات (28,486 بايت) إلى ملف واحد (12,739 بايت) مع بقاء القيم كما هي، دون ادعاء تسريع.

## 3. Cost model (hypothetical, Lab 02) · نموذج التكلفة (افتراضي)

| Policy | Compute (TU) | Storage (TU) | Total (TU) |
|---|---:|---:|---:|
| Always-on (720 h) | 1,440.00 | 20.00 | 1,460.00 |
| Scheduled (67.5 h incl. startup + 30 TU overhead) | 135.00 | 20.00 | 185.00 |

Break-even is 23.25 work h/day; at 23.75 h scheduled becomes 30 TU more expensive. TU are teaching units, not SAR or vendor prices.
التعادل عند 23.25 ساعة يوميًا، وعند 23.75 ساعة يصبح المجدول أغلى بـ30 وحدة. الوحدات تعليمية وليست ريالات أو أسعار مزود.

## 4. Stage durations in the recorded run · مدة المراحل في التشغيل المسجل

Bronze 45 s · dbt (17 commands) 148 s · Day 3 maintenance 118 s · Kafka streaming 27 s · quality gate 73 s · Gold release + recovery 110 s. Wall-clock notebook cell times, including Spark startup; for orientation only.
Bronze ‏45 ثانية، وdbt ‏148 ثانية، وصيانة اليوم الثالث 118 ثانية، والتدفق 27 ثانية، وفحص الجودة 73 ثانية، وبناء Gold والتعافي 110 ثوانٍ. هذه أزمنة الخلايا شاملة إقلاع Spark، للاسترشاد فقط.
