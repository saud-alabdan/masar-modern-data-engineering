# Lab 07 — Gold Release and Recovery · إصدار Gold والتعافي

**Result.** Eight tables were built as one release from the approved inputs (75 trips, 217 events). A deliberate failure after the first table left the previous release selected, and the rebuild got a new id with identical content. All 4 checks passed.
**النتيجة.** بُنيت ثمانية جداول كإصدار واحد من المدخلات المعتمدة (75 رحلة و217 حدثًا). فشل متعمد بعد الجدول الأول أبقى الإصدار السابق مختارًا، وحصلت إعادة البناء على معرف جديد بمحتوى مطابق. نجحت الفحوص الأربعة.

**Row counts.** fact 75 · zone-hour demand 75 · driver-day 18 · zones 3 · drivers 6 · dates 3 · features 3 · labels 3.
**أعداد الصفوف.** الوقائع 75، والطلب لكل منطقة وساعة 75، والسائق لكل يوم 18، والمناطق 3، والسائقون 6، والتواريخ 3، والخصائص 3، والنتائج 3.

Evidence · الدليل: [reports/day05_recovery.json](reports/day05_recovery.json) · [reports/day05_gold_latest.json](reports/day05_gold_latest.json) · [reports/day05_failures/](reports/day05_failures/) · [day05/STUDENT.ipynb](day05/STUDENT.ipynb) cell In [33]

## Questions · الأسئلة

1. The fact has one row per trip (75), while `driver_daily` has one row per driver per day (6 × 3 = 18), which sums back to 75 trips and SAR 1,880.60. The zone is a city proxy, not a real service boundary.
   جدول الوقائع صف لكل رحلة (75)، و`driver_daily` صف لكل سائق في اليوم (6 × 3 = 18) ومجموعه يعود إلى 75 رحلة و1,880.60 ريالًا. والمنطقة هنا مدينة تقريبية لا حدود خدمة حقيقية.
2. A raw join would repeat each fare per GPS event. Counting events per trip first keeps the total, and late trips stay with 0 events. 219 messages contain only 217 distinct events because two were replays.
   الربط المباشر يكرر الأجرة لكل حدث؛ تجميع الأحداث أولًا يحفظ الإجمالي وتبقى الرحلات المتأخرة بصفر أحداث. و219 رسالة فيها 217 حدثًا فقط لأن اثنتين إعادة.
3. The earlier good release stayed selected through `day05_gold_latest.json`, and the failure is recorded in `day05_failures/`. Partial files do not count because the pointer only moves after all eight tables pass.
   بقي الإصدار السليم السابق مختارًا عبر `day05_gold_latest.json` وسُجل الفشل في `day05_failures/`، والملفات الجزئية لا تُعتمد لأن المؤشر لا يتحرك إلا بعد نجاح الجداول الثمانية.
4. Different ids show a genuinely new build, and equal hashes show the same result. This does not prove one transaction across the eight tables, safe concurrent publishing or crash recovery — only same-session recovery.
   اختلاف المعرفات يثبت بناءً جديدًا فعلًا، وتطابق البصمات يثبت النتيجة نفسها، لكنه لا يثبت معاملة واحدة للجداول الثمانية ولا النشر المتزامن ولا التعافي من تعطل الجهاز.
