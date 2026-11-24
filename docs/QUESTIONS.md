# Discovery Questions

| # | Question | Target Document / Page | Answer / Notes |
|---|---|---|---|
| 1 | What is the maximum acceptable latency for the `/health` and core outage prediction endpoints? | Architecture / SLA | Under 200ms p95 for health checks and core queries. |
| 2 | Which cloud providers, regions, and third-party services are within the monitoring scope of OutageOracle? | Scope / Requirements | AWS, GCP, Azure, and major SaaS APIs across primary US and EU regions. |
| 3 | How frequently should external service status feeds and health metrics be polled or ingested? | Ingestion Spec | Every 60 seconds for critical endpoints and every 5 minutes for status feeds. |
| 4 | What historical data retention period is required for past outage records in PostgreSQL? | Database Schema | Minimum 1 year of granular incident records for trend analysis. |
| 5 | How are false positives or brief transient network blips distinguished from confirmed service outages? | Detection Logic | Require at least 2 consecutive failed probes across distinct regions before flagging an outage. |
| 6 | What severity levels should the API classify for detected incidents? | API Specification | Operational, Degraded Performance, Partial Outage, and Major Outage. |
| 7 | Who are the primary consumers of the OutageOracle API, and what authentication method is required? | Security / Auth | Internal dashboards and automated incident bots using API key headers. |
| 8 | How should missing, delayed, or malformed telemetry data from upstream providers be handled? | Reliability / Error Handling | Log schema validation errors, mark source status as unknown, and avoid triggering false alerts. |
| 9 | What alerting thresholds or webhook triggers are needed when an outage probability spikes? | Alerting Guide | Trigger webhook notifications when confidence exceeds 80% for major services. |
| 10 | What are the expected peak request rates and uptime SLAs for the production Render deployment? | Deployment / Ops | Support 100 requests per minute with 99.9% availability. |
