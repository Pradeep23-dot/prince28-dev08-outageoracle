# Dataset: OutageOracle

Learns from 400+ public post-mortems: what broke, why, how it was found and fixed, filtered by root cause and technology.

- **Start target:** 100 post-mortems
- **Full target:** 400+ post-mortems + SRE book chapters (4,000+ pages)
- **Collected by:** `scripts/fetch.py` as `examate-student-bot` (https://github.com/YOUR-GITHUB-USERNAME/prince28-dev08-outageoracle): robots.txt respected, at most 4 requests at once, 0.5 s between requests to one site.

## Where the documents come from

- https://github.com/danluu/post-mortems
- https://sre.google/sre-book/table-of-contents/

## Terms I follow

Each linked article keeps its own copyright: store text for private study, always link the original. SRE book CC BY-NC-ND 4.0.

- Every answer links to the original document.
- No personal data is collected.

<!-- generated:start -->
## Sources (generated 2026-11-23 10:05 UTC by scripts/dataset_report.py)

| Source | Licence / terms | Files | MB | Pages* | How | Fetched | Example URL |
|---|---|---|---|---|---|---|---|
| danluu-links | private study only, always link the original | 96 | 17.7 | 600 | http | 2026-11-23 | https://aws.amazon.com/message/74876-2/ |
| post-mortems | private study only, always link the original | 1 | 0.1 | 32 | git @ 630562e552 | 2026-11-23 | https://github.com/danluu/post-mortems/blob/HEAD/README.md |
| sre-book | CC BY-NC-ND 4.0 | 10 | 0.7 | 88 | http | 2026-11-23 | https://sre.google/sre-book/postmortem-culture/ |

**Total:** 107 files, 18.5 MB, 720 pages. Not downloaded: 1 blocked by robots.txt, 3 failed.

*Pages = PDF pages, plus one page per 3,000 characters of text or HTML.

## Files (first 107 of 107)

The complete list, with a sha256 fingerprint of every file, is `data/manifest.jsonl`.

| # | Title / file | Source | Pages | Fetched | URL |
|---|---|---|---|---|---|
| 1 | Cloudflare | danluu-links | 8 | 2026-11-23 | https://blog.cloudflare.com/1-1-1-1-lookup-failures-on-october-4th-2023/ |
| 2 | Amazon | danluu-links | 10 | 2026-11-23 | https://aws.amazon.com/message/101925/ |
| 3 | Heroku | danluu-links |  | 2026-11-23 | https://status.heroku.com/incidents/1091 |
| 4 | Google | danluu-links | 7 | 2026-11-23 | https://status.cloud.google.com/incident/compute/16007 |
| 5 | Cloudflare | danluu-links | 10 | 2026-11-23 | https://blog.cloudflare.com/18-november-2025-outage/ |
| 6 | PythonAnywhere | danluu-links | 6 | 2026-11-23 | https://blog.pythonanywhere.com/189/ |
| 7 | Google | danluu-links | 4 | 2026-11-23 | https://status.cloud.google.com/incident/storage/19002 |
| 8 | Google | danluu-links | 14 | 2026-11-23 | https://status.cloud.google.com/incident/cloud-networking/19009 |
| 9 | Google | danluu-links | 3 | 2026-11-23 | https://status.cloud.google.com/incidents/1xkAB1KmLrh5g3v9ZEZ7 |
| 10 | NPM | danluu-links |  | 2026-11-23 | https://blog.npmjs.org/post/74949623024/2014-01-28-outage-postmortem.html |
| 11 | TravisCI | danluu-links | 2 | 2026-11-23 | https://web.archive.org/web/20221220114914/https://blog.travis-ci.com/2016-09-30-the-day-we-deleted-our-vm-images/ |
| 12 | GitHub | danluu-links | 9 | 2026-11-23 | https://blog.github.com/2018-10-30-oct21-post-incident-analysis/ |
| 13 | Datadog | danluu-links | 8 | 2026-11-23 | https://www.datadoghq.com/blog/2020-09-25-infrastructure-connectivity-issue/ |
| 14 | GitHub | danluu-links | 5 | 2026-11-23 | https://github.blog/2021-12-01-github-availability-report-november-2021/ |
| 15 | GitHub | danluu-links | 5 | 2026-11-23 | https://github.blog/2022-03-23-an-update-on-recent-service-disruptions/ |
| 16 | Linux | danluu-links | 2 | 2026-11-23 | https://web.archive.org/web/20220320100036/https://lkml.org/lkml/2012/7/1/203 |
| 17 | Amazon | danluu-links | 19 | 2026-11-23 | https://aws.amazon.com/message/2329B7/ |
| 18 | Heroku | danluu-links |  | 2026-11-23 | https://status.heroku.com/incidents/2558 |
| 19 | Linux | danluu-links | 2 | 2026-11-23 | https://web.archive.org/web/20220427012208/https://lkml.org/lkml/2009/1/2/373 |
| 20 | CircleCI | danluu-links | 2 | 2026-11-23 | https://discuss.circleci.com/t/incident-report-november-8-2021-jobs-stuck-in-a-not-running-state/41890 |
| 21 | Amazon | danluu-links | 7 | 2026-11-23 | https://aws.amazon.com/message/4372T8/ |
| 22 | Cloudflare | danluu-links | 7 | 2026-11-23 | https://blog.cloudflare.com/5-december-2025-outage/ |
| 23 | CircleCI | danluu-links | 1 | 2026-11-23 | https://discuss.circleci.com/t/post-incident-report-april-4-2025-delays-in-starting-workflows/53113 |
| 24 | CircleCI | danluu-links | 2 | 2026-11-23 | https://discuss.circleci.com/t/post-incident-report-april-4-2025-circleci-ui-loading-build-triggering-issues/53208 |
| 25 | Amazon | danluu-links | 14 | 2026-11-23 | https://aws.amazon.com/message/56489/ |
| 26 | Amazon | danluu-links | 4 | 2026-11-23 | https://aws.amazon.com/message/656481/ |
| 27 | Amazon | danluu-links | 8 | 2026-11-23 | https://aws.amazon.com/message/67457/ |
| 28 | Google | danluu-links | 4 | 2026-11-23 | https://status.cloud.google.com/incidents/6PM5mNd43NbMqjCZ5REh |
| 29 | Amazon | danluu-links | 3 | 2026-11-23 | https://aws.amazon.com/message/74876-2/ |
| 30 | CircleCI | danluu-links | 7 | 2026-11-23 | https://status.circleci.com/incidents/8rklh3qqckp1 |
| 31 | Cloudflare | danluu-links | 5 | 2026-11-23 | https://web.archive.org/web/20211015231917/https://blog.cloudflare.com/a-byzantine-failure-in-the-real-world/ |
| 32 | CCP Games | danluu-links | 2 | 2026-11-23 | https://community.eveonline.com/news/dev-blogs/about-the-boot.ini-issue/ |
| 33 | Mozilla | danluu-links | 2 | 2026-11-23 | https://web.archive.org/web/20250303152906/https://hacks.mozilla.org/2019/07/add-ons-outage-post-mortem-result/ |
| 34 | GitHub | danluu-links | 7 | 2026-11-23 | https://github.blog/news-insights/company-news/addressing-githubs-recent-availability-issues-2/ |
| 35 | GitHub | danluu-links | 6 | 2026-11-23 | https://github.blog/news-insights/company-news/addressing-githubs-recent-availability-issues/ |
| 36 | TUI | danluu-links | 7 | 2026-11-23 | https://assets.publishing.service.gov.uk/media/604f423be90e077fdf88493f/Boeing_737-8K5_G-TAWG_04-21.pdf |
| 37 | the 27th leap second on 2016-12-31T23:59:60Z | danluu-links |  | 2026-11-23 | https://hpiers.obspm.fr/iers/bul/bulc/bulletinc.52 |
| 38 | Cloudflare | danluu-links | 8 | 2026-11-23 | https://blog.cloudflare.com/cloudflare-incident-march-21-2025/ |
| 39 | Cloudflare | danluu-links | 8 | 2026-11-23 | https://blog.cloudflare.com/cloudflare-incident-on-february-6-2025/ |
| 40 | Cloudflare | danluu-links | 9 | 2026-11-23 | https://blog.cloudflare.com/cloudflare-incident-on-january-24th-2023/ |
| 41 | Cloudflare | danluu-links | 9 | 2026-11-23 | https://blog.cloudflare.com/cloudflare-incident-on-june-20-2024/ |
| 42 | Cloudflare | danluu-links | 7 | 2026-11-23 | https://blog.cloudflare.com/cloudflare-incident-on-october-30-2023/ |
| 43 | Cloudflare | danluu-links | 10 | 2026-11-23 | https://blog.cloudflare.com/cloudflare-outage-february-20-2026/ |
| 44 | Cloudflare | danluu-links | 3 | 2026-11-23 | https://web.archive.org/web/20211016040522/https://blog.cloudflare.com/cloudflare-outage-on-july-17-2020/ |
| 45 | Cloudflare | danluu-links | 4 | 2026-11-23 | https://web.archive.org/web/20220621124002/https://blog.cloudflare.com/cloudflare-outage-on-june-21-2022/ |
| 46 | rust-lang | danluu-links |  | 2026-11-23 | https://blog.rust-lang.org/inside-rust/2023/07/21/crates-io-postmortem.html |
| 47 | Razorpay | danluu-links | 3 | 2026-11-23 | https://web.archive.org/web/20250207075402/https://razorpay.com/blog/day-of-rds-multi-az-failover/ |
| 48 | Cloudflare | danluu-links | 6 | 2026-11-23 | https://blog.cloudflare.com/deep-dive-into-cloudflares-sept-12-dashboard-and-api-outage/ |
| 49 | Keepthescore | danluu-links | 1 | 2026-11-23 | https://web.archive.org/web/20201101133510/https://keepthescore.co/blog/posts/deleting_the_production_database/ |
| 50 | Google | danluu-links | 5 | 2026-11-23 | https://web.archive.org/web/20151008061104/http://code.google.com/p/chromium/issues/detail?id=165171 |
| 51 | Google | danluu-links | 2 | 2026-11-23 | https://cloud.google.com/blog/products/infrastructure/details-of-google-cloud-gcve-incident |
| 52 | rust-lang | danluu-links |  | 2026-11-23 | https://blog.rust-lang.org/inside-rust/2023/02/08/dns-outage-portmortem.html |
| 53 | GitHub | danluu-links | 6 | 2026-11-23 | https://github.blog/news-insights/the-library/dns-outage-post-mortem/ |
| 54 | GitHub | danluu-links | 7 | 2026-11-23 | https://github.blog/news-insights/the-library/downtime-last-saturday/ |
| 55 | Google | danluu-links | 3 | 2026-11-23 | https://status.cloud.google.com/incidents/eo76pxZiDgWVz4z3kmUv |
| 56 | GitHub | danluu-links | 6 | 2026-11-23 | https://github.blog/news-insights/company-news/february-service-disruptions-post-incident-analysis/ |
| 57 | Google | danluu-links | 96 | 2026-11-23 | https://status.cloud.google.com/incidents/fmEL9i2fArADKawkZAa2 |
| 58 | GitHub | danluu-links | 4 | 2026-11-23 | https://github.blog/news-insights/company-news/github-availability-report-august-2024/ |
| 59 | GitHub | danluu-links | 6 | 2026-11-23 | https://github.blog/news-insights/company-news/github-availability-report-february-2026/ |
| 60 | GitHub | danluu-links | 5 | 2026-11-23 | https://github.blog/news-insights/company-news/github-availability-report-may-2021/ |
| 61 | GitHub | danluu-links | 4 | 2026-11-23 | https://github.blog/news-insights/company-news/github-availability-report-october-2020/ |
| 62 | GitHub | danluu-links | 7 | 2026-11-23 | https://github.blog/news-insights/the-library/github-availability-this-week/ |
| 63 | Cloudflare | danluu-links | 3 | 2026-11-23 | https://web.archive.org/web/20211104160742/https://blog.cloudflare.com/how-and-why-the-leap-second-affected-cloudflare-dns/ |
| 64 | Heroku | danluu-links | 4 | 2026-11-23 | https://blog.heroku.com/how-i-broke-git-push-heroku-main |
| 65 | OWASA | danluu-links | 2 | 2026-11-23 | https://web.archive.org/web/20210226170650/https://indyweek.com/news/archives/human-error-caused-owasa-fluoride-overdose-owasa-sorry/ |
| 66 | Turso | danluu-links | 2 | 2026-11-23 | https://blog.turso.tech/incident-2023-12-04-data-leak-and-loss-in-some-free-tier-databases-7cba5bc7 |
| 67 | GoCardless | danluu-links | 8 | 2026-11-23 | https://gocardless.com/blog/incident-review-api-and-dashboard-outage-on-10th-october/ |
| 68 | Interface Message Processor | danluu-links | 6 | 2026-11-23 | https://en.wikipedia.org/wiki/Interface_Message_Processor |
| 69 | TravisCI | danluu-links | 5 | 2026-11-23 | https://www.traviscistatus.com/incidents/khzk8bg4p9sy |
| 70 | Knight Capital | danluu-links | 17 | 2026-11-23 | https://dougseven.com/2014/04/17/knightmare-a-devops-cautionary-tale/ |
| 71 | Cloudflare | danluu-links | 8 | 2026-11-23 | https://blog.cloudflare.com/major-data-center-power-failure-again-cloudflare-code-orange-tested/ |
| 72 | FirstEnergy / General Electric | danluu-links | 27 | 2026-11-23 | https://en.wikipedia.org/wiki/Northeast_blackout_of_2003 |
| 73 | incident.io | danluu-links | 4 | 2026-11-23 | https://incident.io/blog/one-two-skip-a-few |
| 74 | Facebook | danluu-links | 3 | 2026-11-23 | https://engineering.fb.com/2021/10/05/networking-traffic/outage-details/ |
| 75 | Stack Overflow | danluu-links | 1 | 2026-11-23 | https://web.archive.org/web/20201020103424/https://stackstatus.net/post/96025967369/outage-post-mortem-august-25th-2014 |
| 76 | Cloudflare | danluu-links | 3 | 2026-11-23 | https://web.archive.org/web/20221112015610/https://blog.cloudflare.com/partial-cloudflare-outage-on-october-25-2022/ |
| 77 | Cloudflare | danluu-links | 10 | 2026-11-23 | https://blog.cloudflare.com/post-mortem-on-cloudflare-control-plane-and-analytics-outage/ |
| 78 | Allegro | danluu-links |  | 2026-11-23 | https://allegro.tech/2018/08/postmortem-why-allegro-went-down.html |
| 79 | longer write-up | danluu-links | 8 | 2026-11-23 | https://web.archive.org/web/20260315142701/https://www.henricodolfing.com/2019/06/project-failure-case-study-knight-capital.html |
| 80 | Firefox | danluu-links | 3 | 2026-11-23 | https://web.archive.org/web/20250314111052/https://hacks.mozilla.org/2022/02/retrospective-and-technical-details-on-the-recent-firefox-outage/ |
| 81 | ARPANET | danluu-links | 8 | 2026-11-23 | https://datatracker.ietf.org/doc/html/rfc789.html |
| 82 | Cloudflare | danluu-links | 8 | 2026-11-23 | https://blog.cloudflare.com/route-leak-incident-january-22-2026/ |
| 83 | Etsy | danluu-links | 8 | 2026-11-23 | https://web.archive.org/web/20211201033341/https://codeascraft.com/2012/01/23/solr-bittorrent-index-replication/ |
| 84 | WebKit code repository | danluu-links | 5 | 2026-11-23 | https://web.archive.org/web/20210306015541/https://digital.ai/catalyst-blog/subversion-sha1-collision-problem-statement-prevention-and-remediation-options |
| 85 | Azure | danluu-links | 8 | 2026-11-23 | https://azure.microsoft.com/en-us/blog/summary-of-windows-azure-service-disruption-on-feb-29th-2012/ |
| 86 | TravisCI | danluu-links | 3 | 2026-11-23 | https://www.traviscistatus.com/incidents/sxrh0l46czqn |
| 87 | posted | danluu-links | 5 | 2026-11-23 | https://web.archive.org/web/20250408175304/https://hacks.mozilla.org/2019/05/technical-details-on-the-recent-firefox-add-on-outage/ |
| 88 | Google | danluu-links | 1 | 2026-11-23 | https://googleblog.blogspot.com/2009/01/this-site-may-harm-your-computer-on.html |
| 89 | Google | danluu-links | 2 | 2026-11-23 | https://googleblog.blogspot.com/2014/01/todays-outage-for-several-google.html |
| 90 | Cloudflare | danluu-links | 3 | 2026-11-23 | https://web.archive.org/web/20211006135542/https://blog.cloudflare.com/todays-outage-post-mortem-82515/ |
| 91 | Microsoft | danluu-links | 4 | 2026-11-23 | https://azure.microsoft.com/en-us/blog/update-on-azure-storage-service-interruption/ |
| 92 | PagerDuty | danluu-links |  | 2026-11-23 | https://status.pagerduty.com/incidents/vbp7ht2647l8 |
| 93 | Google | danluu-links | 3 | 2026-11-23 | https://status.cloud.google.com/incidents/vLsxuKoRvykNHW3nnhsJ |
| 94 | consequences. | danluu-links |  | 2026-11-23 | https://www.youtube.com/watch?v=msXRFJ2ar_E |
| 95 | TravisCI | danluu-links | 3 | 2026-11-23 | https://www.traviscistatus.com/incidents/z2b3lz2kwcfp |
| 96 | GoCardless | danluu-links | 3 | 2026-11-23 | https://gocardless.com/blog/zero-downtime-postgres-migrations-the-hard-parts/ |
| 97 | README.md | post-mortems | 32 | 2026-11-23 | https://github.com/danluu/post-mortems/blob/HEAD/README.md |
| 98 | Chapter 22 - Addressing Cascading Failures | sre-book | 19 | 2026-11-23 | https://sre.google/sre-book/addressing-cascading-failures/ |
| 99 | Chapter 12 - Effective Troubleshooting | sre-book | 13 | 2026-11-23 | https://sre.google/sre-book/effective-troubleshooting/ |
| 100 | Appendix C - Example Incident State Document | sre-book | 10 | 2026-11-23 | https://sre.google/sre-book/embracing-risk/ |
| 101 | Chapter 13 - Emergency Response | sre-book | 8 | 2026-11-23 | https://sre.google/sre-book/emergency-response/ |
| 102 | Appendix D - Example Postmortem | sre-book | 3 | 2026-11-23 | https://sre.google/sre-book/example-postmortem/ |
| 103 | Chapter 21 - Handling Overload | sre-book | 10 | 2026-11-23 | https://sre.google/sre-book/handling-overload/ |
| 104 | Chapter 14 - Managing Incidents | sre-book | 5 | 2026-11-23 | https://sre.google/sre-book/managing-incidents/ |
| 105 | Chapter 6 - Monitoring Distributed Systems | sre-book | 10 | 2026-11-23 | https://sre.google/sre-book/monitoring-distributed-systems/ |
| 106 | Chapter 15 - Postmortem Culture | sre-book | 5 | 2026-11-23 | https://sre.google/sre-book/postmortem-culture/ |
| 107 | Chapter 16 - Tracking Outages | sre-book | 4 | 2026-11-23 | https://sre.google/sre-book/tracking-outages/ |
<!-- generated:end -->

## Known gaps

## Known gaps

- **4 post-mortems short of the 100-incident start target:** Fetched **107 files** total (**720 pages**, **18.5 MB**), consisting of **96 incident links**, **1 index file** (`danluu/post-mortems`), and **10 SRE book chapters**.
- **4 URLs excluded during fetch:** **1** blocked by `robots.txt` and **3** failed HTTP requests.
- **9 empty/non-text extractions:** **8 URLs** returned blank page counts (JS-rendered status pages like Heroku/PagerDuty/Allegro or non-HTML bulletins) and **1 URL** (`#94`) is a YouTube video link.
- **Remaining toward full target (400+ post-mortems / 4,000+ pages):** **~304+ post-mortems** (mostly in the `Uncategorized` section of `danluu/post-mortems`) and **~38 SRE book chapters/appendices** (~3,280 pages remaining).
