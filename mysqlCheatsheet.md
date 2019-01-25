# MySQL cheatsheet

## Links
[MySQL server has gone away](https://dev.mysql.com/doc/refman/8.0/en/gone-away.html)
[variables - options - commands reference](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html)

## Service - daemon management
```bash
sudo service mysql status
sudo service mysql start

# also
/etc/init.d/mysqld stop
# start mysql server
/etc/init.d/mysqld start

# mysqladmin cli utility
# https://www.tecmint.com/mysqladmin-commands-for-database-administration-in-linux/

# display my.cnf location(s) and to determine the default command option and system variable values used by the server.
> mysqld --verbose --help

mysqladmin -u root -p version 
mysqladmin -u root -p extended-status

# safely shutdown mysql server:
mysqladmin -u root -p shutdown
```  

## Connect - backup - restore - linux CLI
```bash
mysql -u toto -p -h haproxy -P33306
mysql -h  <host_name> -u <user_name> -p<password> <db_name>
mysql --default-character-set=utf8 -u <username> -p -h <host_name>

# backup:
mysqldump -u root -p[root_password] -h [hostname] [database_name] > dumpfilename.sql

# restore:
mysql -u root -p[root_password] [database_name] < dumpfilename.sql

```  

## Various sql
```sql
-- create db statement
create database swap_test
character set utf8
collate utf8_general_ci;

## ALTER TABLE examples
----------------------------------------------------
alter table segments drop column `player_selection`;
alter table segments drop column `player_selection_id`;
alter table segments
	add column `player_selection` tinyint(4) NOT NULL after `active`,
	add column `player_selection_id` tinyint(4) NOT NULL after `player_selection`;
ALTER TABLE `revision`
  MODIFY `publication_date` datetime NULL,
  MODIFY `published_until` datetime NULL;


-- disregard constraints DANGEROUS!!!
SET FOREIGN_KEY_CHECKS=0; 
truncate table cm_pages_content;
truncate table cm_pages;
SET FOREIGN_KEY_CHECKS=1;

# metadata, describe, etc  
>SHOW DATABASES;
>SELECT DATABASE();  # show currently selected db
>SHOW TABLES;
>DESCRIBE pet;  # describe table pet
# Shows the CREATE TABLE statement that creates the named table. To use this statement, you must have some privilege for the table. This statement also works with views.
>SHOW CREATE TABLE tbl_name 
# show @@GLOBAL server variables
>SHOW GLOBAL VARIABLES   
# show @@SESSION server variables
>SHOW VARIABLES
# select to show specific server variables
SELECT @@GLOBAL.max_allowed_packet, @@GLOBAL.wait_timeout, @@GLOBAL.log_error, @GLOBAL.interactive_timeout, @@GLOBAL.max_connections;
#  The optional GLOBAL keyword aggregates the values over all connections, and SESSION shows the values for the current connection.
>SHOW STATUS
-- version info
>SHOW VARIABLES LIKE "%version%";

## CREATE USER - grants examples
------------------------------------------
CREATE USER 'toto'@'%' IDENTIFIED BY '123';

# means NO PRIVILEDGES to this user
GRANT USAGE ON `toto.* TO 'toto'@'%';

# grant all privilidges to a db
GRANT ALL PRIVILEGES ON `toto`.* TO 'toto'@'%';

GRANT UPDATE ON ppvdev.akamai_ids TO 'ppvdevwww'@'%';







-- Paging in MySQL Command-Line Client (http://codehill.com/2014/02/paging-mysql-command-line-client/)
-- ======================================
-- The pager command lets you select a program to use as a pager. In this case we are using less. 
-- The -S argument instructs less to disable line wraps, and -X instructs it disable clearing of the screen when less exits.
-- Now when you view a SELECT query result you can scroll vertically and horizontally using the arrow keys as well as the spacebar, 
-- page up and page down keys. You can also search the query results by pressing the slash key '/' and enter the term to search for. 
-- For example to search for codehill enter /codehill. And to quit press the 'q' key.
-- So execute in mysql prompt the following...
> pager less -S -X

-- How do I see what character set a MySQL database / table / column is?
-- http://stackoverflow.com/questions/1049728/how-do-i-see-what-character-set-a-mysql-database-table-column-is
-- For DB
SELECT default_character_set_name FROM information_schema.SCHEMATA 
WHERE schema_name = "schemaname";

-- for table
SELECT CCSA.character_set_name FROM information_schema.`TABLES` T,
       information_schema.`COLLATION_CHARACTER_SET_APPLICABILITY` CCSA
WHERE CCSA.collation_name = T.table_collation
  AND T.table_schema = "schemaname"
  AND T.table_name = "tablename";
  
-- for column 
SELECT character_set_name FROM information_schema.`COLUMNS` 
WHERE table_schema = "schemaname"
  AND table_name = "tablename"
  AND column_name = "columnname";
  

-- 1. Searching for Text in Stored Procedures and Functions in MySQL
-- =================================================================

-- http://zachzimm.com/blog/?p=23

SELECT * 
FROM mysql.proc 
WHERE lower(CONVERT(body using utf8)) 
        LIKE '%stored-function-name-in-lowercase-here%';

select * from information_schema.columns where column_name like '%priv%';
select * from information_schema.columns where data_type like '%enum%';

-- show table size
SELECT table_name AS "Table", 
round(((data_length + index_length) / 1024 / 1024), 2) "Size in MB" 
FROM information_schema.TABLES 
WHERE table_schema = "ppvdev";

show table status;

-- index size
-- http://aadant.com/blog/2014/02/04/how-to-calculate-a-specific-innodb-index-size/
analyze table lg_osb;

SELECT
       sum(stat_value) pages,
       index_name,
       sum(stat_value) * @@innodb_page_size size
FROM
       mysql.innodb_index_stats
WHERE
           table_name = 'lg_osb'
       AND database_name = 'e_care'
       AND stat_description = 'Number of pages in the index'
GROUP BY
       index_name;

show index from lg_osb;

-- excel concatenate to mysql statement
=CONCATENATE("INSERT INTO `pm_upgrade_route`  (`stype` , `stype_paytv`, `addon_init`,`descr_offer_init`,`stype_trans`,`stype_paytv_trans`,`addon`,`descr_offer`,`path_prior`,`price_diff`,`descr_diff`) VALUES (""";B2;""",""";C2;""",""";D2;""",""";E2;""",""";F2;""",""";G2;""",""";H2;""",""";I2;""",""";J2;""",""";K2;""",""";L2;""");")
  

-- wordpress: make dev installation (other url)
-- https://codex.wordpress.org/Running_a_Development_Copy_of_WordPress
SELECT * FROM acc_options WHERE option_name = "home" OR option_name = "siteurl";
UPDATE acc_options SET option_value = "http://dev.accepted.gr/" WHERE option_name = "home" OR option_name = "siteurl"
```






## Where is my.cnf file
```bash
1. There is no internal MySQL command to trace this, it's a little too abstract. The file might be in 5 (or more?) locations, and they would all be valid because they load cascading.

/etc/my.cnf
/etc/mysql/my.cnf
$MYSQL_HOME/my.cnf
[datadir]/my.cnf
~/.my.cnf

Those are the default locations MySQL looks at. If it finds more than one, it will load each of them & values override each other (in the listed order, I think). Also, the --defaults-file parameter can override the whole thing, so... basically, it's a huge pain in the butt.

2. You can also run from cli:
>mysqladmin --help
or 
> mysqld --verbose --help

It displays:
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf 

3. cli:
>find / -name my.cnf
```  
