# Postgres Cheatsheet

## Functions
```sql
-- find tables which names contains specific text
select table_schema,
       table_name
from information_schema.tables
where table_name like 'payment%'
      and table_schema not in ('information_schema', 'pg_catalog')
      and table_type = 'BASE TABLE'
      and table_name like '%xxx%'
order by table_name,
         table_schema;

-- Get 1st day current of month
date_trunc('month', current_date)

-- test membership in array
-- (some_query_alias.array_of_ids is produced by select array_agg(some_id) as array_of_ids)
... where ... and id_field in (select unnest(some_query_alias.array_of_ids))

-- generate a 32 length UUID
select uuid_generate_v4()

```
