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

## D04 · Docker, Compose and tests

### The image

```text
IMAGE              ID             DISK USAGE   CONTENT SIZE   EXTRA
outageoracle:dev   380e35681929        300MB           70MB
uid=999(app) gid=999(app) groups=999(app)
```

- Size: 300 MB (target: under 400 MB)
- First build: 24.6 s. After changing one line of code: 10.0 s.
- Which step said CACHED, and why? The dependency installation layer (`[builder 4/6] uv sync`) said CACHED because `pyproject.toml` and `uv.lock` were unchanged, so Docker reused the cached layer and only rebuilt from `COPY . /app` onward.

### The stack

```text
NAME                     IMAGE                    COMMAND                  SERVICE   CREATED         STATUS       PORTS
outageoracle-api-1       outageoracle-api         "sh -c 'exec uvicorn…"   api       2 minutes ago   Up 2 minutes (healthy)     0.0.0.0:8000->8000/tcp, [::]:8000->8000/tcp
outageoracle-db-1        pgvector/pgvector:pg17   "docker-entrypoint.s…"   db        2 minutes ago   Up 2 minutes (healthy)     0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp
outageoracle-migrate-1   outageoracle-migrate     "sh -c 'alembic upgr…"   migrate   2 minutes ago   Exited (0) 2 minutes ago
outageoracle-redis-1     redis:7-alpine           "docker-entrypoint.s…"   redis     2 minutes ago   Up 2 minutes (healthy)     0.0.0.0:6379->6379/tcp, [::]:6379->6379/tcp

migrate-1  | records=20 inserted=20 updated=0 unchanged=0
migrate-1  | records=20 inserted=0 updated=0 unchanged=20
```

### Breaking the database on purpose

```text
db stopped:  /health → {"status":"ok","app":"outageoracle","env":"local"}
db stopped:  /ready  → {"status":"not_ready","checks":{"database":false}}
db started:  /ready  → {"status":"ready","checks":{"database":true}}
db started:  /health → {"status":"ok","app":"outageoracle","env":"local"}
```

Why should a load balancer stop sending traffic here but not restart the app?
The FastAPI process itself is still alive and healthy, so restarting the container will not fix an external database outage and only causes unnecessary downtime. Failing `/ready` tells the load balancer to pause routing traffic until Postgres recovers on its own.

### Tests and coverage

```text
uv run poe test              → 16 passed in 0.70s
uv run poe test-integration  → 7 passed in 8.42s
coverage TOTAL, unit only    → 83%
coverage TOTAL, with integration tests → 92%
```

Two files or lines my coverage report says are never run, and whether they matter:
`src/outageoracle/logging_setup.py` (lines 16–28) configures production JSON log formatting that isn't triggered in the local test environment (low risk, though worth a small unit test), while `src/outageoracle/db/repository.py` (lines 32, 36, 66) covers optional query filter branches that matter if users filter by those fields in production.
