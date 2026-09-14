# Lab 03 Notes

## Practice Questions

### 1. Why are 144 staging receipts not 144 trips?

The Bronze data contains intentional redeliveries of the same trips. Each trip can therefore appear in more than one receipt. The 144 staging receipts represent 72 distinct business trips. Silver reduces these repeated receipts to one business record per `trip_id`.

### 2. What could happen to trip counts when the driver table has a duplicate key?

A duplicate `driver_id` can cause one trip to match multiple driver rows during the join. This can multiply trip rows and inflate counts and measures. Duplicate driver keys should therefore be rejected before enrichment.

### 3. A June 1 trip arrives on June 4. Which date should a demand report use, and which should a freshness test use?

The demand report should use the trip's local business date, which is June 1. The freshness test should use the arrival or ingestion date, which is June 4. Business reporting follows when the trip happened, while freshness follows when the data arrived.

### 4. Why is a changed fare for the same trip not an ordinary replay?

An ordinary replay contains the same business content as the original receipt. A changed fare means the business content has changed for the same `trip_id`. This is a conflict or correction and should not be silently treated as a duplicate.

### 5. Why can two correct runs have different log versions but identical business content?

Delta log versions represent table transaction history. A successful rerun can create a new transaction even when the resulting business content is unchanged. Therefore, different transaction versions can still produce identical business data.

### 6. Why would joining raw GPS events triple the base-trip fare sum? Describe a safe alternative.

Each base trip has three GPS events. Joining trip-level fare directly to the event-level GPS data creates three rows for each trip, so the same fare is counted three times.

A safe alternative is to aggregate GPS events to the trip grain before joining them to trip-level data. Another option is to keep GPS at its event grain in a separate dataset.

### 7. Which event would be missed by a strict maximum-event-date filter?

A late-arriving event whose event date is equal to the current maximum event date would be missed if the filter only selects dates strictly greater than the previous maximum. The filter would therefore fail to capture newly arrived data for an already-seen event date.

### 8. Which completed-trip columns must not be used as features for a prediction made at trip start?

Columns that are only known after the trip is completed must not be used for a prediction made at trip start. This includes completed-trip outcomes such as `fare_sar`, `distance_km`, `duration_seconds`, and the trip end timestamp.

Using these fields would introduce information from the future into the prediction.
