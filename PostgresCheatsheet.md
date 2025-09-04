# Postgres Cheatsheet

## Functions
```SQL

SET lock_timeout = '5s';

-- set statement timeout
SET statement_timeout = '15min';
SHOW statement_timeout;
-- no timeout!
SET statement_timeout = 0;

-- Get 1st day current of month
date_trunc('month', current_date)

-- test membership in array
-- (some_query_alias.array_of_ids is produced by select array_agg(some_id) as array_of_ids)
... where ... and id_field in (select unnest(some_query_alias.array_of_ids))

-- generate a 32 length UUID
select uuid_generate_v4()

-- retrieve sequence's last used value
txn=# select nextval('notifications_notificationtypesetting_id_seq');
 nextval
---------
      36

-- show current database
postgres=# select current_database();
current_database
------------------
postgres

```

## PostgreSQL catalog queries
```SQL
-- find tables which names contains specific text
select table_schema,
       table_name,
       table_catalog
from information_schema.tables
where table_name like 'payment%'
      and table_schema = 'public'
      and table_type = 'BASE TABLE'
      and table_name like '%xxx%'
order by table_name,
         table_schema;

-- Similar to the above (find list of db tables) using Postegresql's catalog view
SELECT *
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY tablename;

-- Count estimates for all DB tables
SELECT  n.NspName AS Schema_Name, c.RelName AS Table_Name, c.RelTuples::int AS estimated_rows
FROM    pg_class                AS c
        LEFT JOIN pg_namespace  AS n    ON n.oid = c.RelNameSpace
WHERE   NspName = 'public'
        AND RelKind='r'
ORDER   BY N DESC;

-- Source text of trigger function
SELECT pg_get_functiondef(p.oid) AS function_definition
FROM pg_proc p
WHERE p.proname = 'update_statistics';

-- Find if a trigger function is used on table(s)
SELECT
    schemaname AS schema_name,
    tablename AS table_name,
    triggername AS trigger_name,
    proname AS function_name,
    tgtype,
    CASE
        WHEN tgtype & 2 = 2 THEN 'BEFORE'
        WHEN tgtype & 64 = 64 THEN 'INSTEAD OF'
        ELSE 'AFTER'
    END AS trigger_timing,
    CASE
        WHEN tgtype & 4 = 4 THEN 'INSERT'
        WHEN tgtype & 8 = 8 THEN 'DELETE'
        WHEN tgtype & 16 = 16 THEN 'UPDATE'
        WHEN tgtype & 12 = 12 THEN 'INSERT OR DELETE'
        WHEN tgtype & 20 = 20 THEN 'INSERT OR UPDATE'
        WHEN tgtype & 24 = 24 THEN 'DELETE OR UPDATE'
        WHEN tgtype & 28 = 28 THEN 'INSERT OR DELETE OR UPDATE'
    END AS trigger_event,
    CASE
        WHEN tgtype & 1 = 1 THEN 'ROW'
        ELSE 'STATEMENT'
    END AS trigger_level
FROM pg_trigger t
JOIN pg_class c ON t.tgrelid = c.oid
JOIN pg_proc p ON t.tgfoid = p.oid
WHERE p.proname = 'update_statistics';

-- Identify long running queries
SELECT
  pid,
  now() - pg_stat_activity.query_start AS duration,
  query,
  state
FROM pg_stat_activity
WHERE (now() - pg_stat_activity.query_start) > interval '10 minute';

-- Kill query
SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE pid = 12345;

-- kill all other sessions in a database (You have to be superuser to use pg_terminate_backend)
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity WHERE
-- don't kill my own connection!
pid <> pg_backend_pid()
-- don't kill the connections to other databases
AND datname = 'database_name';

-- Kill all queries on a specific table (very dangerous!)
SELECT pg_terminate_backend(pid)
FROM pg_locks l
JOIN pg_class c ON l.relation = c.oid
WHERE c.relname = 'resources_translation_pt';

-- Show locks with blocking relationships
WITH lock_info AS (
    SELECT
        l.pid,
        l.mode,
        l.granted,
        l.relation,
        l.page,
        l.tuple,
        l.virtualxid,
        l.transactionid,
        l.classid,
        l.objid,
        l.objsubid,
        l.virtualtransaction,
        l.waitstart
    FROM pg_locks l
)
SELECT
    DISTINCT
    l1.pid AS blocking_pid,
    l2.pid AS waiting_pid,
    l1.mode AS blocking_mode,
    l2.mode AS waiting_mode,
    c.relname AS table_name,
    a1.usename AS blocking_user,
    a2.usename AS waiting_user,
    a1.application_name AS blocking_app,
    a2.application_name AS waiting_app,
    a1.state AS blocking_state,
    a2.state AS waiting_state,
    a1.query AS blocking_query,
    a2.query AS waiting_query
FROM lock_info l1
JOIN lock_info l2 ON (
    l1.relation = l2.relation
    AND l1.granted = true
    AND l2.granted = false
)
JOIN pg_class c ON l1.relation = c.oid
JOIN pg_stat_activity a1 ON l1.pid = a1.pid
JOIN pg_stat_activity a2 ON l2.pid = a2.pid
WHERE c.relkind IN ('r', 'p')
  AND c.relname NOT LIKE 'pg_%'

-- Check locks on a specific table
SELECT
    l.pid,
    l.mode,
    l.granted,
    a.usename,
    a.application_name,
    a.client_addr,
    a.state,
    a.query_start,
    a.query
FROM pg_locks l
JOIN pg_class c ON l.relation = c.oid
JOIN pg_stat_activity a ON l.pid = a.pid
WHERE c.relname = 'resources_translation_pt'  -- Replace with your table name
  AND c.relkind IN ('r', 'p')
ORDER BY l.pid;

-- Show how long locks have been held
SELECT
    l.pid,
    l.mode,
    l.granted,
    c.relname AS table_name,
    a.usename,
    a.application_name,
    a.state,
    a.query_start,
    EXTRACT(EPOCH FROM (now() - a.query_start)) AS duration_seconds,
    a.query
FROM pg_locks l
JOIN pg_class c ON l.relation = c.oid
JOIN pg_stat_activity a ON l.pid = a.pid
WHERE c.relkind IN ('r', 'p')
  AND c.relname NOT LIKE 'pg_%'
  AND a.query_start IS NOT NULL
ORDER BY duration_seconds DESC;


```

## DDL
```SQL
-- drop table column
alter table webhooks_webhook drop column if exists public_id;

-- drop constraint from table
ALTER TABLE cart DROP CONSTRAINT cart_shop_user_id_key;

-- reset sequence from given value (36)
ALTER SEQUENCE public.notifications_notificationtypesetting_id_seq RESTART 36

-- create table with select ... into
select t.id, row_number() OVER (order by t.id) as row_no
into plurals_tr_translations
from resources_translation t
inner join projects_project p on p.id = t.project_id
where t.source_entity_id in (select id from resources_sourceentity
                                where pluralized = True)
    and t.language_id <> p.source_language_id;

```

## Other
```SQL
-- Get the row number for every record (DOES ORDERING OF WHOLE QUERYSET)
select t.id, row_number() OVER (order by t.id) as row_no
from resources_translation t
inner join projects_project p on p.id = t.project_id
where t.source_entity_id in (select id from resources_sourceentity
                                where pluralized = True)
    and t.language_id <> p.source_language_id;



```

## psql CLI
```bash
psql -h localhost -p 5432 -U username -W dbname
# prompts for password...
```

```
# (use/connect database db_name)
\c db_name

# (database list)
\l

# describe table
\d <table name>

# (pattern search in listing)
\dt *lang*

# (How to display the function, procedure, triggers source code in postgresql?)
\sf <fuction_or_trigger_or_proc_name>

# To get information about current connection from psql command prompt:
\conninfo

# To change user:
\c - a_new_user
# The ‘-’ is substitute for current database. You can write this to change database and user:
\c a_new_database a_new_user

# Using SQL to get information:
SELECT current_user;

# Connect as Unix postgres superuser to psql and create a db and a user for it
sudo -u postgres psql
postgres=# create database mydb;
postgres=# create user myuser with encrypted password 'mypass';
postgres=# grant all privileges on database mydb to myuser;

# dump db objects
pg_dump txc > out_file
# import from db dump
psql -Uuser_name -W txc < in_file

# connect to another db to drop xxx db
\c postgres
# drop to delete all db objects, then recreate to import from file
drop database xxx;
CREATE DATABASE xxx OWNER = user_name;
# quit
\q

# drop all objects owned by user/role - didn’t work
drop owned by user_name;

# measure time of every statement
\timing;



```
