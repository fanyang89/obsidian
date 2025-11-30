```
SELECT  type,  event_time,  query_duration_ms,  query,  read_rows,  tables
FROM clusterAllReplicas(default, system.query_log)
WHERE has(databases, 'cqgo') AND (event_time >= (now() - toIntervalMinute(3))) AND type='QueryFinish'
ORDER BY query_duration_ms DESC
LIMIT 10
```
