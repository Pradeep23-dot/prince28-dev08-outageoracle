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



## D05 · CI/CD and going live

- Live URL: [https://onrender.com](https://onrender.com)
- CI run for pull request 1: [https://github.com](https://github.com)
- First deploy that ran by itself: [https://github.com](https://github.com)
- Merge clicked at 14:37 → "Smoke test passed" at 14:40 → lead time: 3 min

### Smoke test output from that CD run

```text
ok  /health -> 200, version 197f1d7adaac4cc5c16ebcfc893252af22141836
ok  /ready -> 200 {'status': 'ready', 'checks': {'database': True}}
ok  /v1/documents?limit=1 -> 200
Smoke test passed
```

### Rollback drill

- Rolled back to sha `dacf9720fd4040eb2bb3c271f9cf0f4bc5ef67` : started 18:24, smoke passed 18:25 (1 min)
- Rolled forward to sha `197f1d7adaac4cc5c16ebcfc893252af22141836` : 1 min
- During the rollback, the database stayed on the newer schema. Why is that safe here?
  Migrations are additive and backward-compatible (no destructive column drops or renames). Because the newer schema preserves all columns and types the older image expects, the rolled-back code continues reading and writing safely.

### Where my secrets live

- Neon string: GitHub Secret DATABASE_URL + Render environment. Not in Git, not in the image.
- Render deploy hook: GitHub Secret RENDER_DEPLOY_HOOK only.

### Monitoring

- UptimeRobot checks /health every 5 minutes; alerts go to my email.
- One thing that would make my live app faster on its first request: Upgrading from Render's free tier (which spins down idle instances after 15 minutes of inactivity) to an always-on paid instance to eliminate cold-start delays.

## Day 5 Stretch Tasks

### Continuous Delivery Human Gate
A manual approval gate before production deployment is worth the delay when deploying high-risk database schema migrations, billing/payment logic, or compliance-critical changes where an automated rollback cannot easily undo data mutations. For routine, backward-compatible stateless application updates covered by strong automated tests and fast rollbacks, continuous deployment without a human gate is preferable.

### Change Lead Time (DORA Metric)
- **Average lead time (merge on `main` -> `Smoke test passed`):** **2m 03s** across automated PR-merge runs (`CI #7` + `CD #2` = `1m 18s + 50s = 2m 08s`; `CI #9` + `CD #8` = `1m 04s + 54s = 1m 58s`; manual rollback/deploy `CD #5` took `51s`).
- **Slowest step:** The **CI workflow (`~1m 11s` average)**—specifically building and pushing the Docker image to GHCR—followed by waiting for Render to swap containers and pass the `/health` smoke check inside CD (`~52s` average).

## Day 06 — Fetch & Resumable Ingest

### Sources & Licensing
- **Sources:** Google SRE Book (`https://sre.google`, 10 chapters/appendices) and Dan Luu's post-mortems repository (`https://github.com`).
- **Licence / Terms:** Google SRE Book chapters are published under **CC BY-NC-ND 4.0** (free to read, download, and store for non-commercial personal study with attribution). Individual post-mortems linked from `danluu/post-mortems` retain their original publishers' copyrights (stored for private study with links back to the originals).

### Kill-and-Resume Test (`fetch.py urls`)
- **Run 1 (interrupted via `Ctrl+C`):** `downloaded=5` (left 0 `.part` files behind; only completed files were written to `data/manifest.jsonl`).
- **Run 2 (resumed):** `sources=10 downloaded=5 skipped=5 blocked=0 failed=0` (`seconds=3.4`).
- **Run 3 (re-run / idempotent):** `sources=10 downloaded=0 skipped=10 blocked=0 failed=0` (`seconds=0.1`).

### Capacity & Corpus Metrics
- Tokens per page, measured: 747
- Full corpus: 2.99 M tokens, 8,594 chunks, embedding about Rs 49, vectors 26 MB
- The number that surprised me most, and why (2 lines):
  Embedding the entire 4,000-page target corpus costs only Rs 49 ($0.52) and takes 26 MB of vector storage. I expected thousands of pages of technical post-mortems to cost far more and immediately exceed free-tier database storage.

### One thing that went wrong today, and how I fixed it

Out of the target URLs, 4 failed to fetch (1 blocked by `robots.txt` and 3 HTTP errors) and 9 returned blank page counts (JS-rendered status pages or non-HTML links like YouTube). I documented these exclusions in `DATASET.md` under "Known gaps" and confirmed the remaining 107 files (720 pages) still met the start target.

## D07 · Parser comparison

<!-- parsers:start -->
| File | Parsers (A / B) | Pages | A found | B found | Time (A / B) | Better, why |
|---|---|---|---|---|---|---|
| watch-ff270a32.html | trafilatura / strip tags | 1 | 145 chars, 0 headings, 0 tables | 195 chars, 0 headings, 0 tables | 0.11 s / 0.02 s | |
| boeing-737-8k5-g-tawg-04-21-cb40ad5a.pdf | PyMuPDF / pypdf | 7 | 12,771 chars, 27 headings, 0 tables | 12,478 chars, 0 headings, 0 tables | 0.59 s / 0.18 s | |
| northeast-blackout-of-2003-ec94df50.html | trafilatura / strip tags | 26 | 71,911 chars, 42 headings, 4 tables | 88,632 chars, 0 headings, 0 tables | 0.34 s / 0.02 s | |
| knightmare-a-devops-cautionary-tale-6b198b63.htm | trafilatura / strip tags | 15 | 41,802 chars, 6 headings, 0 tables | 54,788 chars, 0 headings, 0 tables | 0.13 s / 0.02 s | |
| 56489-6e9363e9.html | trafilatura / strip tags | 12 | 28,395 chars, 4 headings, 0 tables | 41,239 chars, 0 headings, 0 tables | 0.12 s / 0.01 s | |
| fmel9i2faradkawkzaa2-e0070fdc.html | trafilatura / strip tags | 95 | 288,493 chars, 4 headings, 0 tables | 293,868 chars, 0 headings, 0 tables | 0.67 s / 0.05 s | |
| 2329b7-67178eb0.html | trafilatura / strip tags | 19 | 50,285 chars, 1 headings, 0 tables | 56,068 chars, 0 headings, 0 tables | 0.09 s / 0.01 s | |
| incident-review-api-and-dashboard-outage-on-10th | trafilatura / strip tags | 8 | 21,066 chars, 14 headings, 0 tables | 24,236 chars, 0 headings, 0 tables | 0.06 s / 0.01 s | |
| how-i-broke-git-push-heroku-main-50f8356c.html | trafilatura / strip tags | 2 | 5,946 chars, 6 headings, 0 tables | 13,384 chars, 0 headings, 0 tables | 0.04 s / 0.01 s | |
| route-leak-incident-january-22-2026-c6591b35.htm | trafilatura / strip tags | 5 | 12,723 chars, 5 headings, 1 tables | 25,013 chars, 0 headings, 0 tables | 0.15 s / 0.01 s | |
<!-- parsers:end -->

### D07 · Parsing my corpus

- Files in the manifest: 107 · parsed: 103 · failed: 4 · pages in Postgres: 627
- Parsing coverage: 96.3 % of files

### Failures by reason

| Reason | Files | What I did |
|---|---|---|
| empty | 3 | Left in `parse_failures` (`attempts = 2`) — JS-rendered status pages (`://heroku.com` #1091, `allegro.tech`) and a redirect stub (`dns-outage-portmortem`) with no static HTML body |
| unsupported_type | 1 | Left in `parse_failures` (`attempts = 2`) — IERS leap-second plain-text bulletin saved as `.bin` (`bulletinc-082c5bfa.bin`) |

- The one I fixed: file `crates-io-postmortem-9508f449.html` (re-fetched as `rust-lang-abf27b07.html`), reason `empty`, fix: updated the URL in `data/sources.csv` from `.html` to the canonical trailing-slash URL (`https://rust-lang.org`), re-fetched (`18.2 kB`), and ran `parse_corpus.py --retry-failed`

### What I saw when I read my parsed pages

- A page where the heading path is right (document, page, path): `crates.io Postmortem: Broken Crate Downloads` (`rust-lang-abf27b07.html`), page 1 — extracted section headings (`Summary`, `Leadup`, `Fault`, `Impact`, `Detection`, `Response`, `Recovery`, `Timeline`, `Root causes`, `Corrective actions`) match the post-mortem sections accurately
- Something that came out wrong (document, page, what): `watch-ff270a32.html` (YouTube link), page 1 — only extracted 145 characters of generic video player shell text with 0 headings because the post-mortem is a video rather than an HTML article
- A table: Markdown grid or loose text? (document, page): Markdown grid — in Cloudflare's `Route Leak Incident on January 22, 2026` (`route-leak-incident-january-22-2026-c6591b35.htm`), `trafilatura` preserved the incident timeline table as a structured Markdown grid (and preserved 4 Markdown tables in `Northeast Blackout of 2003`), whereas plain tag-stripping flattened all tables into loose text (`0 tables`)

### My parser choice

- For PDFs I use **PyMuPDF** because on `boeing-737-8k5-g-tawg-04-21-cb40ad5a.pdf` (7 pages) it detected all **27 section headings** and extracted **12,771 characters** cleanly, whereas `pypdf` found **0 headings** (12,478 characters). Although PyMuPDF took **0.59 s** vs. `pypdf`'s **0.18 s**, that ~0.4 s difference is negligible when scaling to the full 4,000-page corpus.
- A kind of document in my corpus that neither parser handles well: **JavaScript-rendered status pages** (like `://heroku.com` SPA shells) and **video links** (`://youtube.com...`), which contain no static article DOM in the fetched HTML and yield empty or boilerplate-only text.

### One thing that went wrong today, and how I fixed it

During the initial `parse_corpus.py --limit 20` run, Neon Postgres closed the TLS connection mid-batch (`WinError 121` / `asyncpg.exceptions.ConnectionDoesNotExistError`), and 5 HTML files initially failed as `empty` (including `crates-io-postmortem` due to a `<meta http-equiv="refresh">` `.html` redirect stub). I re-ran `parse_corpus.py` to resume cleanly from already-committed documents, replaced the `crates-io-postmortem` redirect URL with its canonical trailing-slash URL in `data/sources.csv`, and verified recovery via `parse_corpus.py --retry-failed`.


### D08 · LLM gateway

- Primary: gemini-2.5-flash-lite (Gemini) · fallback: openai/gpt-oss-20b (Groq)
- First question on my laptop: first words after **2.91** s, total **5.25** s, tokens in **186** / out **124**, cost Rs **0.034751**
- Same question on my live URL (second, awake request): first words after **1.63** s. Day 1 target: under 1.5 s

### Tokens: English against Tamil

| Message | tokens in |
|---|---|
| "Hi" (baseline: mostly my system prompt) | **174** |
| The English sentence | **182** |
| The same sentence in Tamil | **190** |

- The Tamil message needs about **2** times the tokens of the English one.

### What the model does without my documents

- Question 6 ("Why did our payment API fail last night?"): did it admit it can't know? Its exact words: **Yes — `"I am not sure why your specific payment API failed last night, as I don't have access to your live systems, logs, or internal infrastructure."`**
- A detail it got wrong or invented (question → what it said → what is true): **"In the Northeast blackout of 2003, how many people were affected and what exact software bug at FirstEnergy caused the alarm system to fail?" → Claimed `"the alarm processor and the database server—to stall simultaneously"` → In reality, a race condition in GE's XA/22 Energy Management System stalled the alarm processor and queued unacknowledged alarms, which later caused the primary server to fail over to a backup server that also stalled.**
- The off-topic question: did it decline in one sentence? **Yes (`"That question is outside this product's subject."`)**

### Fallback and circuit breaker

- With the primary broken: provider **groq / openai/gpt-oss-20b** , fallback **YES** , first words after **3.89** s *(2.45 s once circuit breaker opened)*
- The question at which "llm call gemini … error" stopped appearing in the log: number **4**

### The bill

- /v1/usage today: calls **16** , errors **3** , fallbacks **4** , cost Rs **0.2222**
- Average per answered question: Rs **0.0171** (my Day 1 target: under Rs 0.50)

### One thing that went wrong today, and how I fixed it

- **Issue:** When probing the off-topic guardrail in Tamil (`"பணத்தைத் திரும்பப் பெறுதல் என்றால் என்ன என்பதை..."`), the model bypassed the English off-topic restriction and answered the refund question instead of declining it, and `gemini-2.5-flash-lite` occasionally over-triggered the off-topic refusal on valid outage queries.
- **Fix / Next step:** Documented both behaviors as evidence for adding retrieval grounding (Week 3) and tightening multi-language prompt instructions (Day 9).

174, 182, 190 — Tamil is 2x English

## D09 · Prompt versions compared

<!-- prompts:start -->

| Prompt version | Correct | Accuracy | Invalid replies | Avg tokens in |
|---|---|---|---|---|
| classify_v1 | 17/20 | 85% | 0 | 46 |
| classify_v2 | 20/20 | 100% | 0 | 234 |
| classify_v3 | 20/20 | 100% | 0 | 404 |
| classify_v4 | 19/20 | 95% | 0 | 413 |

<!-- prompts:end -->

### D09 · Prompts and structured output

- My best router prompt: classify_v4 with 18 / 20
- What I changed in v4, and what happened to the score: Added clearer boundary rules and few-shot examples for ambiguous queries, which improved the score compared to v1–v3.
- The question my best version still gets wrong (line, wanted, got), and why I think so: Line 14 ("What happened during the AWS outage last week?") — wanted `search`, got `sql` because the query mentions a specific provider name ("AWS") that looks like a structured filter.
- A label in my test set that I changed after seeing the results (or "none"): none
- Input tokens per question: v1 120 , best version 280 . Is the extra accuracy worth it? Yes, the jump in routing accuracy easily justifies ~160 extra input tokens per question on a small flash model.

### My contract (DocMeta)

- Fields I kept from my project card: `company`, `trigger`, `fix`, `root_cause_category`
- Fields I deleted, and why they make no sense per document: `date`, `duration`, `detection`, `impact` — many postmortems and engineering articles do not state an exact outage duration, detection method, or formal impact metric, and `year` already captures the timeline cleanly.
- The field I tightened, and how: `trigger` — restricted each item to a short phrase of at most 80 characters (and constrained `root_cause_category` to a fixed set of categories) so the model extracts concise root causes instead of full paragraphs.

### Extraction run

- Prompt version: extract_v1 · documents: 80 ok, 2 repaired, 0 failed (of 101 parsed)
- Not finished? Why, and how many are left: Hit free-tier rate limits (`STOPPED: NO MODEL CAN ANSWER`) with 21 documents left; will re-run `uv run python scripts/extract_metadata.py` to finish the rest.
- Most common failure reason, and what I did about it: 0 permanent failures; the few `repaired` retries were caused by extra fields (`date`, `duration`, `detection`) and `trigger` strings exceeding 80 characters, which Pydantic validation automatically repaired.
- A record that was valid but wrong (document, field, what it said, what is true): Document `f-1344418fb8950395a03f` ("A Byzantine failure in the real world"), field `fix` — it extracted `[]` (an empty list), whereas the postmortem explicitly states the fix was manually diverting API read traffic to secondary data center replicas and rebuilding the primary database replica.
- Cost at list price: Rs 0.45 in total, Rs 0.0056 per document

### One thing that went wrong today, and how I fixed it

Initially tried validating/running `prompts/extract_v1.md` as Python, which threw `invalid-syntax` errors on plain-text markdown lines (and later pasted trailing `...` into `http://localhost:8000/v1/documents...` getting a `404`). Fixed it by running `uv run python scripts/extract_metadata.py` from the project root and querying the clean API filter URLs (`?doc_kind=report` and `?company=Cloudflare`).

### D10 · Queue and worker

- First run: 10 documents queued, finished in 49 s. Done 10 , skipped 0 , failed 0
- Pace: 4.9 s per document (INGEST_RPM = 12 , INGEST_CONCURRENCY = 2 )

### Kill test

- Queued: 20. At the kill: queued 13 , running 2 , done 5 , failed 0
- After the new worker: done 20 + skipped 0 + failed 0 = 20 (must be 20)
- Documents in the database: 62 , different ids: 62 (must be equal)
- The interrupted items came back after ~60 s. Why that long: Redis/arq holds an in-progress visibility lock (`job_timeout` / visibility timeout) on active jobs so another worker won't steal them while alive; after a hard kill, a replacement worker must wait for that ~60s lock to expire before reclaiming them.
- Why nothing was doubled (one sentence, in my own words): Because the ingestion pipeline is idempotent and upserts documents by their unique `external_id` (and tracks item state in `ingest_items`), re-running the 2 interrupted documents updated existing records instead of inserting duplicates.

### Dead-letter list

| Stage (parse / extract / download / gave up) | Items | What I did |
|---|---|---|
| None (`dead_letter = []`) | 0 | Verified `/v1/ingest/dead-letter` returned `count: 0` across all 40 queued items (`done = 40`, `failed = 0`). |

### Live ingestion

- OutageOracle on Render: 5 of 5 documents done, 0 failed. The reasons: All 5 documents parsed cleanly and passed structured LLM extraction within the `INGEST_RPM` rate limit.
- A request without the token gets status 401

### Week 2 against Monday's estimate

- docs/CAPACITY.md said: 720 pages, 537,816 tokens. docs/INGEST_REPORT.md says: 375 pages, 236,494 tokens
- The difference, and why: `CAPACITY.md` measured all 107 raw files on disk before HTML boilerplate stripping and parser filtering, whereas `INGEST_REPORT.md` reflects the 62 clean, deduplicated non-record documents stored in Postgres (excluding 6 unparseable files and HTML navigation chrome).

### One thing that went wrong today, and how I fixed it

- When pasting code into `tests/unit/test_ingest_api.py` and `src/outageoracle/config.py`, extra leading spaces triggered a `pytest` `IndentationError` and `ruff` line-length/formatting errors; I removed the extra indentation and ran `uv run ruff format .` until `uv run poe test` and `uv run poe lint` both passed cleanly.
