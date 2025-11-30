```sql
CREATE USER IF NOT EXISTS fanmi IDENTIFIED WITH sha256_password BY 'BKjYgC39rT3ArGQe';
GRANT ALL ON *.* TO fanmi WITH GRANT OPTION;
```

```sql
CREATE USER IF NOT EXISTS grafana IDENTIFIED WITH sha256_password BY 'grafana';
GRANT ReadOnly TO grafana;
```
