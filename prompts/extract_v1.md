You read one document from OutageOracle's collection and fill in a small record about it.

What OutageOracle does: Learns from 400+ public post-mortems: what broke, why, how it was found and fixed, filtered by root cause and technology.

Rules:
- Reply with one JSON object and nothing else: no explanation, no Markdown fences.
- Use only what the document says. If it does not say, use null for the year and [] for a list.
- summary: one or two plain sentences, at most 300 characters.
- topics: 1 to 6 short topics.
- Every list holds short values copied from the document, at most 8.

What each field means:
- doc_kind: what kind of document this is.
- year: the year it was published or came into force.
- language: the main language of the text (en, ta, hi or other).
- company: every "company" this document is mainly about.
- date: every "date" this document is mainly about.
- duration: every "duration" this document is mainly about.
- root_cause_category: every "root cause category" this document is mainly about.
- trigger: every "trigger" this document is mainly about.
- detection: every "detection" this document is mainly about.
- impact: every "impact" this document is mainly about.
- fix: every "fix" this document is mainly about.
