# Governance · الحوكمة

Short record of how the Masar data is owned, traced, protected and retained. This is a training exercise, not legal advice or a compliance certificate.
سجل مختصر لملكية بيانات مسار وتتبعها وحمايتها والاحتفاظ بها. هذا تمرين تدريبي وليس استشارة قانونية أو شهادة امتثال.

1. **Data and purpose.** Only the synthetic `MASAR_SMALL_V1` fixture (`data/masar-small-v1`) is used, for learning; no real trips, phone numbers, IDs or GPS traces.
   **البيانات والغرض.** نستخدم البيانات الاصطناعية فقط لغرض تعليمي، دون رحلات أو أرقام أو هويات أو مواقع حقيقية.
2. **Roles.** As a single learner I hold all four: owner (approves use), operator (investigates failed runs), steward (explains quality rules) and consumer (uses approved snapshots only). In a real team, the steward and owner would review any rule change.
   **الأدوار.** أقوم بالأدوار الأربعة: المالك والمشغل ومسؤول الجودة والمستخدم، وفي فريق حقيقي يراجع مسؤول الجودة والمالك أي تغيير في القواعد.
3. **Lineage — events.** `gps.ndjson` (SHA-256) → producer acknowledgments → topic / partition / offset → raw Delta + checkpoint → 217-event snapshot (`reports/day04_stream_latest.json`).
   **تتبع الأحداث.** من ملف المصدر وبصمته إلى تأكيدات الإرسال والموضوع والإزاحة، ثم Delta الخام ونقطة التحقق، ثم لقطة 217 حدثًا.
4. **Lineage — trips.** Corrected Silver → isolated candidate → quality policy + GX → quarantine or rechecked 75-trip snapshot → Day 5 input (`reports/day04_quality_latest.json`).
   **تتبع الرحلات.** من Silver المصححة إلى مرشح معزول ثم قواعد الجودة وGX، ثم العزل أو اللقطة المعتمدة (75 رحلة)، ثم مدخل اليوم الخامس.
5. **Access — implemented.** Broker bound to `127.0.0.1` only, fixed synthetic inputs, separate run folders, validation, quarantine and preserved reports. BI exports carry no raw coordinates.
   **الوصول المنفذ.** وسيط على العنوان المحلي فقط، ومدخلات ثابتة، ومجلدات تشغيل منفصلة، وفحص وعزل وتقارير محفوظة، وتصديرات BI بلا إحداثيات خام.
6. **Access — not implemented (proposals).** User authentication, TLS, multi-user permissions and row-level security; a folder name or a GX pass does not enforce them.
   **الوصول غير المنفذ (مقترحات).** المصادقة وTLS وصلاحيات المستخدمين وأمان مستوى الصف، واسم المجلد أو نجاح GX لا يفرضها.
7. **Retention.** Evidence is kept until submission review. Kafka's 168-hour setting is an exercise value, separate from Delta version retention, and the handoff ZIP does not include broker storage.
   **الاحتفاظ.** تُحفظ الأدلة حتى مراجعة التسليم، وإعداد Kafka بـ168 ساعة قيمة تدريبية مستقلة عن احتفاظ Delta، وملف ZIP لا يشمل تخزين الوسيط.
8. **Before real data.** Follow the organization's privacy, access and retention rules and the SDAIA references (S10–S12 in `SOURCES.md`); keep credentials, raw data and broker storage out of Git.
   **قبل البيانات الحقيقية.** اتبع قواعد الخصوصية والصلاحيات والاحتفاظ في الجهة ومراجع سدايا، وأبقِ بيانات الدخول والبيانات الخام خارج Git.
