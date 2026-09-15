# Lab 05 — Streaming from Kafka · استقبال أحداث Kafka

**Result.** Kafka → Spark Streaming → Delta worked with one persistent checkpoint: transport rows 216 → 216 → 218 → 219 while distinct events were 216 → 216 → 216 → 217. All 13 checks passed, and the final snapshot has 217 events linked to trusted trips.
**النتيجة.** نجح المسار من Kafka إلى Spark Streaming ثم Delta بنقطة تحقق واحدة: صفوف الوصول 216 ثم 216 ثم 218 ثم 219، والأحداث المختلفة 216 ثم 216 ثم 216 ثم 217. نجحت الفحوص الـ13، واللقطة النهائية فيها 217 حدثًا مرتبطة برحلات موثوقة.

**Setup note.** Docker was not available, so I ran a native Kafka 4.0.2 broker on `127.0.0.1:9092`.
**ملاحظة الإعداد.** لم يكن Docker متاحًا، فشغّلت وسيط Kafka 4.0.2 مباشرة على `127.0.0.1:9092`.

**Event time vs processing time.** Event time is `event_ts` inside each GPS message (when it happened). Processing time is when Kafka and Spark received it (`broker_timestamp`, `ingested_at`). The late event `SYN_E_LATE001` has an old `event_ts` but arrived last; no window or watermark was applied, so it was kept as a new distinct event.
**وقت الحدث مقابل وقت المعالجة.** وقت الحدث هو `event_ts` داخل الرسالة (متى حدث)، ووقت المعالجة هو متى استقبله Kafka وSpark. الحدث المتأخر `SYN_E_LATE001` وقته قديم لكنه وصل أخيرًا، ولم نطبق نافذة أو Watermark فبقي كحدث جديد مستقل.

Evidence · الدليل: [reports/day04_stream_latest.json](reports/day04_stream_latest.json) · [reports/day04/](reports/day04/) (producer acknowledgments) · [day04/STUDENT.ipynb](day04/STUDENT.ipynb) cell In [29]

## Questions · الأسئلة

1. Nothing new was sent and the checkpoint had already saved its offsets, so it stayed 216. `query_id` stayed the same and `query_run_id` changed; rerunning the whole cell would start a new topic and experiment.
   بقي العدد 216 لأنه لم يُرسل جديد ونقطة التحقق حفظت موقعها؛ `query_id` ثابت و`query_run_id` تغيّر، وإعادة الخلية كاملة تبدأ تجربة وموضوعًا جديدين.
2. Replay sent 2 new messages (new offsets) for existing events: transport key = topic + partition + offset, business key = `event_id`. Producer idempotence only removes retry duplicates, not deliberate resends.
   الإعادة أرسلت رسالتين جديدتين لأحداث موجودة؛ مفتاح الوصول هو الموضوع والجزء والإزاحة، ومفتاح الأعمال `event_id`، وحماية المنتج تمنع تكرار إعادة المحاولة فقط.
3. The late event has a new `event_id`, so it is a real new observation and is kept. No watermark was used, so this does not prove watermark behaviour.
   الحدث المتأخر له معرف جديد فهو ملاحظة جديدة ويُحتفظ به، ولم نستخدم Watermark فلا يثبت ذلك سلوكه.
4. `dropDuplicates` would keep an arbitrary location. `unique_events()` in `src/masar/streaming.py` rejects the same `event_id` with different content before the final `dropDuplicates`.
   `dropDuplicates` يختار موقعًا عشوائيًا، أما `unique_events()` في `streaming.py` فيرفض المعرف نفسه بمحتوى مختلف قبل حذف التكرار.
