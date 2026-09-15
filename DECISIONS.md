# Decisions · القرارات

Architecture, performance and governance choices for the Masar project, with the alternative I rejected. Numbers come from my run; details are in the lab notes, [BENCHMARKS.md](BENCHMARKS.md) and [GOVERNANCE.md](GOVERNANCE.md).
قرارات المعمارية والأداء والحوكمة في مشروع مسار مع البديل المرفوض. الأرقام من تشغيلي، والتفاصيل في ملاحظات اللابات وملفي القياس والحوكمة.

1. **Bronze keeps every delivery; Silver keeps one row per trip.** Cleaning on arrival would hide replays (144 deliveries = 72 trips).
   **Bronze يحفظ كل وصول وSilver صفًا لكل رحلة.** التنظيف عند الاستلام يخفي الإعادات (144 وصولًا = 72 رحلة).
2. **Delta Lake for every layer instead of plain Parquet/CSV.** I chose it for MERGE, time travel, constraints and RESTORE, not speed — CSV was faster on this tiny data.
   **Delta Lake لكل الطبقات بدل Parquet أو CSV.** اخترته لـMERGE والقراءة الزمنية والقيود والاستعادة لا للسرعة، فـCSV كان أسرع هنا.
3. **Resolve revisions before MERGE, never `dropDuplicates`.** Stale values are ignored and same-revision conflicts stop the run (total stayed SAR 1,880.60).
   **حسم المراجعات قبل MERGE لا `dropDuplicates`.** القيم القديمة تُتجاهل والتعارض يوقف التشغيل (بقي الإجمالي 1,880.60 ريالًا).
4. **Aggregate GPS events per trip before joining.** A raw join would triple the fares.
   **تجميع أحداث GPS لكل رحلة قبل الربط.** الربط المباشر يضاعف الأجور ثلاث مرات.
5. **Keep both Spark and dbt for Silver.** They agree on the same digests (72 / 72 / 75 / 75), which is stronger evidence than one route alone.
   **الإبقاء على Spark وdbt معًا.** اتفاقهما على نفس البصمات دليل أقوى من مسار واحد.
6. **Kafka with a persistent checkpoint; deduplicate by `event_id` only after comparing content.** Producer idempotence alone would not catch deliberate replays (219 messages, 217 events).
   **Kafka مع نقطة تحقق دائمة، وحذف التكرار بالمعرف بعد مقارنة المحتوى.** حماية المنتج وحدها لا تكشف الإعادات المقصودة (219 رسالة و217 حدثًا).
7. **Never approve a filtered failed batch.** The clean 75 rows had to pass again as their own candidate before use.
   **لا اعتماد لدفعة فاشلة بعد تصفيتها.** الصفوف النظيفة الـ75 أُعيد فحصها كمرشح مستقل قبل الاستخدام.
8. **Gold is published as a release behind a pointer.** A failed build can never become the selected data.
   **نشر Gold كإصدار خلف مؤشر.** البناء الفاشل لا يصبح البيانات المختارة أبدًا.
9. **AI features use only data available at the cutoff; unknown labels stay null.** Filling them with zero would fake an answer.
   **الخصائص من البيانات المتاحة وقت القطع فقط، والنتائج المجهولة تبقى فارغة.** تعبئتها بالصفر اختلاق للإجابة.
10. **Run natively on Windows (no Docker/WSL) with small portability fixes.** These are an English JVM locale, winutils, a native Kafka broker and forward-slash paths in three helper files; no check or lab rule was changed (details in README).
   **التشغيل مباشرة على Windows بإصلاحات توافق صغيرة.** شملت لغة JVM إنجليزية وwinutils ووسيط Kafka محليًا ومسارات بشرطة أمامية في ثلاثة ملفات، دون تغيير أي فحص أو قاعدة (التفاصيل في README).
