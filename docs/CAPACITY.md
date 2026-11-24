# Capacity plan: OutageOracle

Full corpus target: 400+ post-mortems + SRE book chapters (4,000+ pages)

<!-- generated:start -->
## Measured on my start corpus (2026-11-23 10:05 UTC)

| What | Value |
|---|---|
| Files | 107 |
| Size on disk | 18.5 MB |
| Pages (PDF pages + 1 per 3,000 characters of text/HTML) | 720 |
| Characters of text | 2,151,266 |
| Estimated tokens (characters / 4) | 537,816 |
| Tokens per page | 747 |
| PDFs with almost no text (scanned? OCR is Day 7) | 0 |
| Files not measured (zip, xlsx, unreadable) | 1 |

## Estimate for the full corpus (target: 4,000 pages)

| What | How | Value |
|---|---|---|
| Scale factor | target / measured | x 5.6 |
| Tokens | measured tokens x scale | 2.99 M |
| Raw files | measured MB x scale | 103 MB |
| Chunks | tokens / 400 x 1.15 overlap | 8,594 |
| Embedding cost, one time | tokens x 1.15 x $0.15 per 1M | $0.52 = Rs 49 |
| Vector storage | chunks x 768 numbers x 4 bytes | 26 MB |
| Text in Postgres | characters x scale x 1.2 | 14 MB |

Assumptions: 4 characters per token, 400-token chunks, 768-dimension embeddings at $0.15 per 1M tokens, Rs 95 per US dollar.
<!-- generated:end -->

## What I conclude

1. **No:** One-time embedding for the full 4,000-page corpus (2.99 M tokens, 8,594 chunks) costs **$0.52 (Rs 49)**, which is negligible.
2. **Yes:** Combined vector storage (**26 MB**) and Postgres text (**14 MB**) total **40 MB**, well within a standard free-tier Postgres limit (typically **500 MB** on Supabase/Neon; check your dashboard to confirm exact quota).
3. **Tokens per page (747) / 4,000-page target:** Because HTML pages include navigation boilerplate and 9 fetched links returned empty shells, I will measure exact token counts after HTML text extraction and deduplication.
4. At **10× scale** (~40,000 pages / 29.9 M tokens / ~85,940 chunks), one-time embedding rises to **$5.20 (Rs 490)** and database storage grows to **~400 MB** (**260 MB** vectors + **140 MB** text), approaching a 500 MB free-tier Postgres cap and requiring HNSW/IVFFlat vector indexing plus batched ingestion.
