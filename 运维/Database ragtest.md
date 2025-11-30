```
fanmi@fanyang-legion ~ $ sudo -iu postgres
[postgres@fanyang-legion ~]$ psql -d ragtest -U fanmi
psql (17.5)
Type "help" for help.

ragtest=#

ragtest=# \d document_chunks
                                  Table "public.document_chunks"
    Column    |     Type     | Collation | Nullable |                   Default
--------------+--------------+-----------+----------+---------------------------------------------
 id           | bigint       |           | not null | nextval('document_chunks_id_seq'::regclass)
 document     | text         |           | not null |
 raw_document | text         |           | not null |
 text         | text         |           | not null |
 embedding    | vector(4096) |           |          |
Indexes:
    "document_chunks_pkey" PRIMARY KEY, btree (id)

ragtest=# CREATE INDEX ON document_chunks USING hnsw (embedding halfvec_l2_ops);
NOTICE:  hnsw graph no longer fits into maintenance_work_mem after 7247 tuples
DETAIL:  Building will take significantly more time.
HINT:  Increase maintenance_work_mem to speed up builds.
CREATE INDEX

[postgres@fanyang-legion ~]$ psql -U fanmi ragtest
psql (17.5)
Type "help" for help.

ragtest=# CREATE INDEX ON document_chunks USING hnsw (embedding halfvec_l2_ops);
NOTICE:  hnsw graph no longer fits into maintenance_work_mem after 7247 tuples
DETAIL:  Building will take significantly more time.
HINT:  Increase maintenance_work_mem to speed up builds.
CREATE INDEX
ragtest=# SELECT phase, round(100.0 * blocks_done / nullif(blocks_total, 0), 1) AS "%" FROM pg_stat_progress_create_index;
 phase | %
-------+---
(0 rows)

ragtest=# SELECT phase, round(100.0 * blocks_done / nullif(blocks_total, 0), 1) AS "%" FROM pg_stat_progress_create_index;
 phase | %
-------+---
(0 rows)

ragtest=# SET maintenance_work_mem = '16GB';
SET
ragtest=# SELECT phase, round(100.0 * blocks_done / nullif(blocks_total, 0), 1) AS "%" FROM pg_stat_progress_create_index;
 phase | %
-------+---
(0 rows)

ragtest=# SET max_parallel_maintenance_workers = 16;
SET
ragtest=# SELECT phase, round(100.0 * blocks_done / nullif(blocks_total, 0), 1) AS "%" FROM pg_stat_progress_create_index;
 phase | %
-------+---
(0 rows)

ragtest=# CREATE INDEX ON document_chunks USING hnsw (embedding halfvec_l2_ops);
ERROR:  could not resize shared memory segment "/PostgreSQL.1041338908" to 17176989120 bytes: No space left on device
ragtest=# SET maintenance_work_mem = '8GB';
SET
ragtest=# CREATE INDEX ON document_chunks USING hnsw (embedding halfvec_l2_ops);
CREATE INDEX
ragtest=#
```

```
-> % go run ./cmd/ragctl compute
⠼ Computing embeddings (125089/-, 18 it/s) [1h58m29s]
```

```
ragtest=# DROP TABLE document_chunks ;
```
