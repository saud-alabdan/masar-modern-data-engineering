# Lab 01 — Inspect Sources and Build Bronze · فحص المصادر وبناء Bronze

**Result.** The manifest was verified and the sources hold 72 trips, 6 drivers and 216 GPS events with no broken links. Bronze was written to Delta: trips 144 (base 72 + replay 72, same payload digest), drivers 6, events 216. All 7 checks passed.
**النتيجة.** تحققنا من سجل البيانات، والمصادر فيها 72 رحلة و6 سائقين و216 حدثًا دون روابط مكسورة. كُتبت Bronze في Delta: الرحلات 144 (72 أساسية و72 إعادة بنفس البصمة) والسائقون 6 والأحداث 216. نجحت الفحوص السبعة.

**Quality risks.** Observed: the replay doubled trip deliveries (144 rows for 72 trips), and 10 city labels had spaces or lowercase (" riyadh "). Potential: every CSV value arrives as text, so fares and timestamps can fail typing, and late or corrected files can change past days.
**مخاطر الجودة.** مرصود: الإعادة ضاعفت سجلات الوصول (144 لـ72 رحلة)، و10 أسماء مدن فيها مسافات أو حروف صغيرة. محتمل: كل قيم CSV تصل نصًا فقد تفشل الأجور والأوقات عند التحويل، والملفات المتأخرة أو المصححة قد تغيّر أيامًا سابقة.

Evidence · الدليل: [reports/source_inspection.json](reports/source_inspection.json) · [reports/bronze.json](reports/bronze.json) · [day01/STUDENT.ipynb](day01/STUDENT.ipynb) cells In [3]–In [5], In [9]

## Questions · الأسئلة

1. A delivery record is one Bronze row (144), a business trip is one `trip_id` (72), and a GPS event is a location ping (216, three per trip). Bronze counts deliveries; reports count distinct trips.
   سجل الوصول صف في Bronze (144)، والرحلة معرف `trip_id` واحد (72)، والحدث موقع GPS (216، ثلاثة لكل رحلة). Bronze تعدّ مرات الوصول، والتقارير تعدّ الرحلات الفريدة.
2. Labels such as " riyadh " (4), " jeddah " (3) and " dammam " (3) would split each city into two groups. Normalization belongs in Silver, while Bronze keeps the raw text and source hash.
   كتابات مثل " riyadh " و" jeddah " و" dammam " تقسم المدينة إلى مجموعتين؛ التوحيد مكانه Silver، وتبقى Bronze بالنص الأصلي وبصمة المصدر.
3. SYN_T0001 appears once in `trips.csv` and twice in Bronze (`base_001`, `replay_002`), with `_source_file`, `_source_sha256`, `_batch_id` and `_ingested_at`. Ingestion time is when I loaded it; `start_ts` is when the synthetic trip happened.
   تظهر SYN_T0001 مرة في الملف ومرتين في Bronze مع اسم الملف وبصمته ومعرف الدفعة ووقت الاستلام؛ وقت الاستلام هو وقت التحميل، و`start_ts` وقت حدوث الرحلة الاصطناعية.
4. Break-even is 23.25 work hours/day, and at 23.75 h scheduled compute costs 30 TU more. Startup time and fixed overhead mean switching off is not automatically cheaper; TU are teaching units, not prices.
   نقطة التعادل 23.25 ساعة عمل يوميًا، وعند 23.75 ساعة تصبح الجدولة أغلى بـ30 وحدة؛ وقت الإقلاع والتكلفة الثابتة يجعلان الإيقاف ليس أرخص دائمًا، والوحدات تعليمية لا أسعار.
5. 72 vs 144 rows is a different population; timing may include startup; one sample with caches decides nothing. A fair claim: "same 72 rows, same result, 4 timed runs, on this laptop only".
   مقارنة 72 بـ144 صفًا مقارنة مجموعتين مختلفتين، والتوقيت قد يشمل الإقلاع، وعينة واحدة مع ذاكرة مؤقتة لا تحسم؛ الاستنتاج العادل: نفس 72 صفًا ونفس النتيجة و4 قياسات على هذا الجهاز فقط.
6. Valid files are proven by the manifest hashes, engine execution by real Delta tables and `bronze.json` checks, and course readiness by all five days running end to end — not by a green screenshot.
   صحة الملفات تثبتها بصمات السجل، وتنفيذ المحرك تثبته جداول Delta وفحوص `bronze.json`، وجاهزية الدورة يثبتها تشغيل الأيام الخمسة كاملة، لا لقطة شاشة خضراء.
