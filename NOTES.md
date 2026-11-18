# Engineering notes: OutageOracle

## D03 · PostgreSQL

### Migrations (upgrade, downgrade, upgrade)

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running downgrade f1d555afd0fd -> f09b2e1431a4, add gin index on metadata
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
f09b2e1431a4
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade f09b2e1431a4 -> f1d555afd0fd, add gin index on metadata
f09b2e1431a4 -> f1d555afd0fd (head), add gin index on metadata
<base> -> f09b2e1431a4, create documents and pages
```

### The loader is idempotent

```text
(outageoracle) PS C:\Users\prade\prince28-dev08-outageoracle> uv run python scripts/load_db.py
>> uv run python scripts/load_db.py
records=20 inserted=0 updated=0 unchanged=20
records=20 inserted=0 updated=0 unchanged=20
(outageoracle) PS C:\Users\prade\prince28-dev08-outageoracle> uv run python scripts/load_db.py
>> uv run python scripts/load_db.py
records=20 inserted=0 updated=0 unchanged=20
records=20 inserted=0 updated=0 unchanged=20
(outageoracle) PS C:\Users\prade\prince28-dev08-outageoracle> uv run python scripts/load_db.py
>> uv run python scripts/load_db.py
records=20 inserted=0 updated=1 unchanged=19
records=20 inserted=0 updated=0 unchanged=20
```

### EXPLAIN for ?company=VALUE

```text
-- 1) What the planner chooses on its own:
Seq Scan on documents  (cost=0.00..11.00 rows=1 width=36) (actual time=1.337..1.337 rows=1.00 loops=1)
  Filter: (metadata @> '{"company": "Spotify"}'::jsonb)
  Rows Removed by Filter: 19
  Buffers: shared read=3 dirtied=3
Planning:
  Buffers: shared hit=143 read=8 dirtied=1
Planning Time: 5.681 ms
Execution Time: 1.378 ms

-- 2) Seq scan switched off, to prove the index can be used:
Bitmap Heap Scan on documents  (cost=12.81..16.82 rows=1 width=36) (actual time=1.501..1.502 rows=1.00 loops=1)
  Recheck Cond: (metadata @> '{"company": "Spotify"}'::jsonb)
  Heap Blocks: exact=2
  Buffers: shared hit=3 read=2
  ->  Bitmap Index Scan on ix_documents_metadata_gin  (cost=0.00..12.81 rows=1 width=0) (actual time=1.466..1.466 rows=2.00 loops=1)
        Index Cond: (metadata @> '{"company": "Spotify"}'::jsonb)
        Index Searches: 1
        Buffers: shared hit=1 read=2
Planning:
  Buffers: shared hit=1
Planning Time: 0.076 ms
Execution Time: 1.552 ms
```

Why did the planner choose a Seq Scan on my table? (2 lines, your own words)
The documents table currently only has 20 rows, so it fits inside just a couple of memory pages. Reading the whole table directly in one go has a lower estimated cost than reading the GIN index first and then jumping back to fetch the matching heap pages.


When would it switch to the GIN index? (1 line)
t will switch to the GIN index once the table grows large enough (thousands of rows) that scanning every page becomes slower and more expensive than looking up a selective filter in the index
