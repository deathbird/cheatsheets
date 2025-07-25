# Postgres Cheatsheet

## Functions
```SQL
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

-- Identify long running queries
SELECT
  pid,
  now() - pg_stat_activity.query_start AS duration,
  query,
  state
FROM pg_stat_activity
WHERE (now() - pg_stat_activity.query_start) > interval '10 minute';

-- Kill query
SELECT pg_terminate_backend(<pid>);

-- kill all other sessions in a database (You have to be superuser to use pg_terminate_backend)
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity WHERE
-- don't kill my own connection!
pid <> pg_backend_pid()
-- don't kill the connections to other databases
AND datname = 'database_name';



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
