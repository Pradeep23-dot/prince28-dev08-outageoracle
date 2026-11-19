# prince28-dev08-outageoracle
College Project Repository

# OutageOracle

Learns from 400+ public post-mortems: what broke, why, how it was found and fixed, filtered by root cause and technology.

**Status:** Day 1 foundation (FastAPI skeleton, quality gates). Live URL from Day 5.

## Who it's for

SRE lead at a Chennai payments startup setting up the team's first incident process.

## The problem

## **The problem**

Public post-mortems are scattered across hundreds of company blogs and use different formats, making incident lessons difficult to compare. Important failures involving certificate expiry, DNS outages, and bad configuration pushes are hard to find in one place. SRE teams waste time searching multiple sources instead of learning from previous incidents. OutageOracle brings these post-mortems together, classifies them by root cause and technology, and makes their lessons easy to explore.

## Data sources

Post-mortems linked from the danluu/post-mortems collection (fetched text, links kept), plus Google SRE book chapters on incident management.

- https://github.com/danluu/post-mortems
- https://sre.google/sre-book/table-of-contents/
- Terms: Each linked article keeps its own copyright: store text for private study, always link the original. SRE book CC BY-NC-ND 4.0.

## Hardest problem (my interview story)

Heterogeneous sources: 400 websites with different layouts need robust extraction (readability), and root causes must be classified consistently with structured output so 'all DNS outages' really returns all of them.

## Signature feature

Root-cause explorer: filter outages by cause, technology and year and read the lessons with citations.

## Target metric

Root-cause classification agrees 85%+ with hand labels on 60 post-mortems.

## Run it locally

```bash
uv sync
cp .env.example .env   # Windows: copy .env.example .env
uv run uvicorn outageoracle.api.main:create_app --factory --reload
```

Then open http://localhost:8000/docs

## Checks

```bash
uv run pytest
uv run ruff check .
```
## Run locally in one command

You need Docker Desktop running. From a fresh clone:

```bash
docker compose up --build
```

This starts Postgres (with pgvector), Redis, runs the migrations and loads the sample data once, then starts the API.

- API docs: http://localhost:8000/docs
- Liveness: http://localhost:8000/health
- Readiness (checks the database): http://localhost:8000/ready

Stop with `Ctrl+C`, then `docker compose down`. Add `-v` to also delete the database volume.

## Develop and test

| Task | Mac / Linux | Windows |
|---|---|---|
| Run the API with reload | `make dev` | `uv run poe dev` |
| Unit tests (no network) | `make test` | `uv run poe test` |
| Integration tests (needs `docker compose up`) | `make test-integration` | `uv run poe test-integration` |
| Coverage report | `make cov` | `uv run poe cov` |
| Lint and format check | `make lint` | `uv run poe lint` |
