# Lab 06 — Quality Gate and Quarantine · فحص الجودة والعزل

**Result.** The trusted data passed Great Expectations, and the 82-row mixed candidate failed with 7 rows quarantined and promotion blocked. The rechecked 75-row candidate passed and matches corrected Silver (SAR 1,880.60). All 9 checks passed.
**النتيجة.** نجحت البيانات الموثوقة في Great Expectations، وفشل المرشح المختلط ذو الـ82 صفًا مع عزل 7 صفوف ومنع اعتماده. نجح المرشح المُعاد فحصه (75 صفًا) وطابق Silver المصححة (1,880.60 ريال). نجحت الفحوص التسعة.

**Quarantine reasons (1 each).** Missing trip id, unknown driver, invalid city, fare, distance, duration and timestamp.
**أسباب العزل (سبب لكل صف).** غياب معرف الرحلة، وسائق غير معروف، ومدينة أو أجرة أو مسافة أو مدة أو توقيت غير صالح.

Evidence · الدليل: [reports/day04_quality_latest.json](reports/day04_quality_latest.json) · [reports/day04_quality/](reports/day04_quality/) (`mixed_policy.json`, GX `checkpoint_result.json` per case) · [day04/STUDENT.ipynb](day04/STUDENT.ipynb) cell In [30] · Governance: [GOVERNANCE.md](GOVERNANCE.md)

## Questions · الأسئلة

5. The volume check (expected 75) blocks approval. A low rejection rate says nothing about rows that never arrived.
   فحص الحجم (المتوقع 75) يمنع الاعتماد، ونسبة الرفض المنخفضة لا تكشف الصفوف التي لم تصل أصلًا.
6. 75 accepted + 7 quarantined = 82, but the batch broke row rules and volume, so it must fail. The clean 75 rows were used only after passing again as a separate candidate.
   75 مقبولًا و7 معزولة تساوي 82، لكن الدفعة خالفت القواعد والحجم فيجب أن تفشل، والصفوف النظيفة لم تُستخدم إلا بعد نجاحها كمرشح مستقل.
7. Both ages use the scenario clock: 240 s since the last delivery and 33,540 s since the last event — not network latency. A distance of 0 means the same city mix as the baseline, not proof that the data is correct.
   العمران محسوبان بساعة السيناريو: 240 ثانية منذ آخر وصول و33,540 ثانية منذ آخر حدث، وليسا زمن شبكة. ومسافة الصفر تعني توزيع مدن مطابقًا للأساس، لا دليلًا على صحة البيانات.
8. The ZIP carries Delta tables, reports and checkpoints but not Kafka storage. A folder name or a GX pass is not access control or legal compliance.
   ملف ZIP ينقل جداول Delta والتقارير ونقاط التحقق دون تخزين Kafka، واسم المجلد أو نجاح GX ليس صلاحيات ولا امتثالًا نظاميًا.
