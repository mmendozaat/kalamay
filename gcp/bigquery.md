### ARRAYS

```
SELECT DISTINCT
  visitId,
  h.page.pageTitle
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`,
UNNEST(hits) AS h
WHERE visitId = 1501570398
LIMIT 10
```

hits is an array column in ga_sessions_20170801
arrays have MODE = REPEATED

### STRUCT

```
SELECT
  visitId,
  totals.*,
  device.*
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
WHERE visitId = 1501570398
LIMIT 10
```

structs have TYPE = RECORD

### unpacking array-struct

```
#standardSQL
SELECT race, participants.name
FROM racing.race_results
CROSS JOIN
race_results.participants # full STRUCT name
```

treat struct as an inline view
This is a correlated cross join which only unpacks the elements associated with a single row.
