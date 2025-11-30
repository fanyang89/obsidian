#db #duckdb

```sql
CREATE TABLE test AS SELECT * FROM read_json_auto('./test.json', format = 'newline_delimited', sample_size = -1);
```
