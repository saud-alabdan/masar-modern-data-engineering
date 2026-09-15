# Lab 08 — BI and AI Serving · تجهيز بيانات BI وAI

**Result.** All 16 serving checks passed. The BI summary gives Dammam 25 trips / SAR 670.40, Jeddah 25 / 625.20 and Riyadh 25 / 585.00, for 75 trips and SAR 1,880.60. The three feature rows use only past data, and the three labels stay `UNOBSERVED` with empty targets.
**النتيجة.** نجحت فحوص التقديم الـ16. ملخص BI: الدمام 25 رحلة / 670.40 ريال، وجدة 25 / 625.20، والرياض 25 / 585.00، بإجمالي 75 رحلة و1,880.60 ريالًا. صفوف الخصائص الثلاثة تعتمد على بيانات سابقة فقط، والنتائج الثلاث بقيت `UNOBSERVED` بأهداف فارغة.

Evidence · الدليل: [reports/day05_serving_latest.json](reports/day05_serving_latest.json) · [reports/serving/](reports/serving/) (8 CSV exports) · [day05/STUDENT.ipynb](day05/STUDENT.ipynb) cell In [34]

## Questions · الأسئلة

5. 670.40 + 625.20 + 585.00 = 1,880.60 over 75 trips. A trip starting at 23:55 belongs to its start day (June 1). A combined mean must be weighted by trip count: (1470·8 + 1350·8 + 1230·8) / 24 = 1,350 s.
   670.40 + 625.20 + 585.00 = 1,880.60 لـ75 رحلة. الرحلة التي تبدأ 23:55 تُحسب ليوم بدايتها (1 يونيو)، والمتوسط المجمع يُوزن بعدد الرحلات: (1470×8 + 1350×8 + 1230×8) ÷ 24 = 1,350 ثانية.
6. No — it was not available on June 2. The cutoff is 2026-06-04 03:05 UTC, history is the 24 hours before it, and the target is 04:00 UTC. The only inputs are zone, completed trips, average duration and history flag; the average is history, not a prediction.
   لا، لأنها لم تكن متاحة في 2 يونيو. وقت القطع 2026-06-04 03:05 UTC، والتاريخ هو 24 ساعة قبله، والهدف الساعة 04:00 UTC، والمدخلات: المنطقة وعدد الرحلات ومتوسط المدة ووجود التاريخ، والمتوسط تاريخ لا تنبؤ.
7. The target hour has not happened yet, so labels stay null. Zero would claim no trips occurred, and an accuracy score on made-up targets would be fake. In the CSV, null is an empty field.
   ساعة الهدف لم تحدث بعد فتبقى النتائج فارغة؛ الصفر يعني ادعاء عدم وجود رحلات، ودرجة الدقة على أهداف مختلقة غير صحيحة. والفراغ يظهر في CSV كحقل فارغ.
8. `day05_handoff.zip` has the pointer plus all workspace tables, releases, exports and reports. Notebooks, notes and the Day 2 dbt workspace stay outside it. Submitted: the `develop` branch of my fork [saud-alabdan/masar-modern-data-engineering](https://github.com/saud-alabdan/masar-modern-data-engineering/tree/develop), with the programme, SDAIA Academy link and `#SDAIAAcademy` in the README.
   يحتوي `day05_handoff.zip` على المؤشر وجداول مساحة العمل والإصدارات والتصديرات والتقارير، وتبقى الدفاتر والملاحظات ومساحة dbt خارجه. التسليم: فرع `develop` في نسختي من مستودع الدورة، مع اسم البرنامج ورابط أكاديمية سدايا والوسم `#SDAIAAcademy` في README.
