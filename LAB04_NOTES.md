# Lab 04 — Delta Transactions and Maintenance · معاملات Delta والصيانة

**Result.** The correction moved SYN_T0001 from SAR 18.00 to 23.00 (revision 2), keeping 75 trips and bringing the total to SAR 1,880.60. All 6 transaction checks and 7 maintenance checks passed, and trusted Silver stayed unchanged.
**النتيجة.** نقل التصحيح أجرة SYN_T0001 من 18.00 إلى 23.00 ريالًا (المراجعة 2)، وبقيت الرحلات 75 وأصبح الإجمالي 1,880.60 ريالًا. نجحت فحوص المعاملات الستة وفحوص الصيانة السبعة، وبقيت Silver الموثوقة دون تغيير.

**What I saw.** Silver versions went 1 → 2 (correction) → 3 and 4 (replays, no value changed). Compaction turned 4 files into 1, and the recovery copy went WRITE → DELETE → RESTORE as versions 0, 1, 2.
**ما لاحظته.** تدرجت نسخ Silver من 1 إلى 2 (التصحيح) ثم 3 و4 (إعادات دون تغيير قيم). الضغط حوّل 4 ملفات إلى ملف واحد، ونسخة الاستعادة مرت بـ WRITE ثم DELETE ثم RESTORE كنسخ 0 و1 و2.

Evidence · الدليل: [reports/day03_transactions.json](reports/day03_transactions.json) · [reports/day03_maintenance_latest.json](reports/day03_maintenance_latest.json) · [day03/STUDENT.ipynb](day03/STUDENT.ipynb) cells In [25]–In [26]

## Questions · الأسئلة

1. No — `source_revision=2` is the source's version of one trip, while Delta went from table version 1 to 2 when it was applied.
   لا، `source_revision=2` نسخة المصدر لرحلة واحدة، أما Delta فانتقل من النسخة 1 إلى 2 عند تطبيقه.
2. The correction updated an existing trip instead of adding one; +5.00 on SYN_T0001 matches 1,875.60 → 1,880.60.
   التصحيح عدّل رحلة موجودة ولم يضف رحلة، وفرق 5.00 ريالات يطابق الانتقال من 1,875.60 إلى 1,880.60.
3. The old 18.00 has a lower revision so it is ignored; 24.00 with the same revision is a conflict and stops before MERGE.
   الأجرة القديمة 18.00 مراجعتها أقل فتُتجاهل، أما 24.00 بنفس المراجعة فتعارض يوقف التنفيذ قبل MERGE.
4. A missing-package error proves nothing; the proof is the `fare_nonnegative` violation with version and digest unchanged, so neither row was saved.
   خطأ غياب مكتبة لا يثبت شيئًا؛ الدليل هو خرق قيد `fare_nonnegative` مع بقاء النسخة والبصمة، أي لم يُحفظ أي صف.
5. The schema copy leaves out SYN_T0002 so adding it back gives 75; adding it to a full copy would create a duplicate trip (76 rows).
   نسخة المخطط تستثني SYN_T0002 فإضافته تعيدها إلى 75، أما إضافته لنسخة كاملة فتنتج رحلة مكررة (76 صفًا).
6. `surcharge_sar` is a separate column on one row and does not change `fare_sar`; the 74 nulls mean "not supplied", not zero.
   `surcharge_sar` عمود مستقل في صف واحد ولا يغير الأجرة، والقيم الفارغة الـ74 تعني "غير مرسلة" لا صفرًا.
7. The version never goes back: RESTORE is recorded as a new version 2 after the DELETE, while time travel only reads.
   رقم النسخة لا يتراجع؛ الاستعادة تُسجل كنسخة جديدة رقم 2 بعد الحذف، بينما القراءة الزمنية للقراءة فقط.
8. Not a failure: it was a dry run with 168-hour retention, the files are too new, and 0 files were deleted.
   ليس فشلًا؛ كانت معاينة فقط بفترة احتفاظ 168 ساعة، والملفات أحدث من ذلك، ولم يُحذف أي ملف.
