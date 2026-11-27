# Ingestion report

<!-- generated:start -->
_Generated 27 Nov 2026 14:35 from the database._

| What | Count |
|---|---|
| Documents in the database | 62 |
| Pages | 375 |
| Tokens of text (characters ÷ 4) | 236,494 |
| Files the parser gave up on | 6 |
| Documents with an extracted record | 62 |
| Documents whose extraction failed | 0 |

| Queue | Count |
|---|---|
| Ingest jobs started | 3 |
| Items finished (done or skipped) | 40 |
| Items that needed more than one try | 2 |
| Items in the dead-letter list | 0 |

Model calls for extraction: 164 · tokens in 337,802 · tokens out 22,855 · cost at list price Rs 15.06

Last job `551d60d91455450097047e52c3878ca5`: 20 requested, 20 ok, 0 failed, 6.2 minutes.
<!-- generated:end -->

## What I learnt

(three lines, yours)
## What I learnt

- Our ingested corpus reached 62 documents, 375 pages, and 236,494 text tokens (~631 tokens/page), compared to Monday's initial raw measurement of 720 pages and 537,816 tokens (~747 tokens/page) before HTML boilerplate removal and parser filtering (6 files skipped).
- Killing the `arq` worker mid-job proved crash recovery and idempotency: the 2 interrupted items had their Redis locks expire and were reclaimed (`attempts = 2`) with 0 duplicates and 0 dead-letter failures.
- Decoupling ingestion onto a background worker kept the API responsive (`HTTP 202`) while sustaining ~12 LLM extraction calls per minute (164 calls costing Rs 15.06 across the corpus).
