---
title: "Essential Postgresql Queries"
date: 2019-03-26T23:13:20+05:30
draft: false
toc: false
images:
  - https://picsum.photos/1024/768/?random
tags: 
  - postgresql
---

This post has a collated list of postgresql queries (taken from multiple sources) which are used on an almost daily basis.

***

## Age of unfrozen rows

``` psql
SELECT c.oid::regclass as table_name,
       greatest(age(c.relfrozenxid),age(t.relfrozenxid)) as age
FROM pg_class c
LEFT JOIN pg_class t ON c.reltoastrelid = t.oid
WHERE c.relkind IN ('r', 'm');
```

``` psql
SELECT datname, age(datfrozenxid) FROM pg_database;
```

The age column measures the number of transactions from the cutoff XID to the current transaction's XID. Here, the cutoff XID, `relfrozenxid` is the freeze cutoff XID that was used by the last whole-table VACUUM; likewise `datfrozenxid` is the lower bound on unfrozen XIDs for that database.

***

## Currently running queries

``` psql
SELECT pid, query, query_start FROM pg_stat_activity where state = 'active';
```

***

## Replication Status on Master/Slave

``` psql
# check replication/streaming status on MASTER
SELECT application_name,client_addr,state, \\
                 (sent_offset - (replay_offset - (sent_xlog - replay_xlog) * 255 * 16 ^ 6 ))::text AS byte_lag \\
                  FROM (SELECT \\
application_name,client_addr,state,sync_state,sent_lsn,write_lsn,replay_lsn, \\
                          ('x' || lpad(split_part(sent_lsn::text,   '/', 1), 8, '0'))::bit(32)::bigint AS sent_xlog, \\
                          ('x' || lpad(split_part(replay_lsn::text, '/', 1), 8, '0'))::bit(32)::bigint AS replay_xlog, \\
                          ('x' || lpad(split_part(sent_lsn::text,   '/', 2), 8, '0'))::bit(32)::bigint AS sent_offset, \\
                          ('x' || lpad(split_part(replay_lsn::text, '/', 2), 8, '0'))::bit(32)::bigint AS replay_offset \\
                        FROM pg_stat_replication) \\
                  AS s;

# check time elapsed since last replication on SLAVE
select now() - pg_last_xact_replay_timestamp() AS replication_delay;
```

***

## Time when (auto)vacuum & (auto)analyze last ran

``` psql
select relname, last_vacuum, last_autovacuum, last_analyze, last_autoanalyze from pg_stat_user_tables;
```

***

## Number of live tuples and dead tuples

``` psql
SELECT pg_stat_get_live_tuples(c.oid) AS n_live_tup, pg_stat_get_dead_tuples(c.oid) AS n_dead_tup FROM pg_class c where c.relname = '<relname>';
```