---
title: CLICKHOUSE
---

# CLICKHOUSE

```sql
ALTER TABLE commits DROP COLUMN IF EXISTS id;

CREATE TABLE commits ( `id` String, `repository` String, `tagø` String, `hash` String )
ENGINE = ReplacingMergeTree
PRIMARY KEY (repository, tag, hash);

DESCRIBE TABLE file('data.parquet', Parquet);
INSERT INTO tickets FROM INFILE 'tickets.parquet' FORMAT Parquet;
```
