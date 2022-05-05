# Postgres Cheatsheet

## Functions
```sql
-- Get 1st day current of month
date_trunc('month', current_date)

-- test membership in array
-- (some_query_alias.array_of_ids is produced by select array_agg(some_id) as array_of_ids)
... where ... and id_field in (select unnest(some_query_alias.array_of_ids))


```
