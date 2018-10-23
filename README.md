
## Postgres queries

Frequently used postgres queries:

-->show running queries (pre 9.2)
SELECT procpid, age(clock_timestamp(), query_start), usename, current_query 
FROM pg_stat_activity 
WHERE current_query != '<IDLE>' AND current_query NOT ILIKE '%pg_stat_activity%' 
ORDER BY query_start desc;

-->show running queries (9.2)
SELECT pid, age(clock_timestamp(), query_start), usename, query 
FROM pg_stat_activity 
WHERE query != '<IDLE>' AND query NOT ILIKE '%pg_stat_activity%' 
ORDER BY query_start desc;

-->kill running query
SELECT pg_cancel_backend(procpid);

-->kill idle query
SELECT pg_terminate_backend(procpid);


-->All database users
select * from pg_stat_activity where current_query not like '<%';

-->All databases and their sizes
select * from pg_user;

-->All tables and their size, with/without indexes
select datname, pg_size_pretty(pg_database_size(datname))
from pg_database
order by pg_database_size(datname) desc;

-->Cache hit rates (should not be less than 0.99)
SELECT sum(heap_blks_read) as heap_read, sum(heap_blks_hit)  as heap_hit, (sum(heap_blks_hit) - sum(heap_blks_read)) / sum(heap_blks_hit) as ratio
FROM pg_statio_user_tables;


-- Dump database on remote host to file
$ pg_dump -U username -h hostname DatabaseName > ambari_db.sql

-- Import dump into existing database
$ psql -d dbName -f dump.sql
