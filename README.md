# Splunk Dashboards Portfolio
 
> Transforming raw operational data into decisions — real dashboards built to detect, correlate, and resolve performance issues faster.
 
![SPL](https://img.shields.io/badge/SPL-Search_Processing_Language-black)
![Splunk](https://img.shields.io/badge/Built_with-Splunk-green)
![Focus](https://img.shields.io/badge/Focus-Observability_%26_Root_Cause_Analysis-blue)
 
---
 
## Why this exists
 
Most engineers can build dashboards. Very few can build observability systems that actually drive decisions.

In large-scale environments, teams are flooded with alerts but still lack clarity on what truly matters — what is impacting users, where the problem originates, and how to act quickly.

This portfolio exists to demonstrate that difference.

It showcases how I design and build Splunk-based observability solutions that:
- Detect issues before they become outages
- Correlate data across multiple layers (network, application, external services)
- Turn raw telemetry into clear, actionable insights

Each project reflects real-world problems solved end-to-end — from data onboarding and normalization to advanced SPL, dashboard design, and measurable operational impact.

This is not a collection of dashboards.

It’s a demonstration of how I approach observability as a system — focused on reducing noise, accelerating root cause analysis, and improving end-user experience at scale.
 
---
 
## Dashboards
 
### 01 — Web Error Rate Monitor
 
**Problem:** The team was detecting HTTP 5xx spikes too late — often learning about outages from users before any internal alert fired. Investigation required jumping between multiple tools, adding 30–40 minutes to every incident.
 
**Approach:** Built a real-time dashboard correlating error rate, response time, and traffic volume by host and endpoint. Added threshold-based color coding so degradation is visible at a glance before it becomes an outage.
 
**Impact:**
- Mean time to detection dropped from ~35 min to under 4 min
- Eliminated manual log correlation during incidents
- Adopted by the on-call team as the primary triage screen
 
**Key SPL:**
```spl
index=web_logs status>=500
| timechart span=5m count as errors by host
| appendcols
    [search index=web_logs
    | timechart span=5m count as total by host]
| eval error_rate=round((errors/total)*100, 2)
| where error_rate > 5
| sort -error_rate
```
 
---
 
### 02 — Infrastructure Health Overview
 
**Problem:** Operations team had no unified view of server health. CPU spikes, memory pressure, and disk saturation were managed reactively — often discovered only after services started degrading.
 
**Approach:** Aggregated host metrics across environments into a single-pane dashboard with trend analysis and anomaly flags. Added a capacity forecasting panel using linear projection over 7-day windows.
 
**Impact:**
- Identified 3 servers trending toward disk saturation 2 weeks before impact
- Reduced unplanned maintenance windows by enabling proactive intervention
- Gave management a single dashboard for infrastructure status in weekly reviews
 
**Key SPL:**
```spl
index=os_metrics metric_name=cpu_usage
| timechart span=1h avg(metric_value) as avg_cpu by host
| eval status=case(
    avg_cpu >= 90, "critical",
    avg_cpu >= 75, "warning",
    true(), "healthy")
| where status != "healthy"
| sort -avg_cpu
```
 
---
 
### 03 — Application Transaction Correlation
 
**Problem:** A critical business application was experiencing intermittent slowdowns with no clear pattern. Database, middleware, and frontend logs lived in separate systems with no unified view, making root cause analysis a multi-hour process.
 
**Approach:** Built a correlated transaction dashboard pulling from 5 log sources — app server, database, load balancer, cache, and external API — into a single timeline. Designed to let engineers trace a slow transaction from frontend response time back to its root cause in a single interface.
 
**Impact:**
- Reduced root cause analysis time from 3+ hours to under 20 minutes
- Discovered a database connection pool exhaustion pattern not visible in any single source
- Pattern became a template for incident playbooks
 
**Key SPL:**
```spl
index=app_logs OR index=db_logs OR index=lb_logs transaction_id=*
| eval layer=case(
    index="app_logs", "Application",
    index="db_logs", "Database",
    index="lb_logs", "Load Balancer")
| stats
    avg(response_time) as avg_ms,
    max(response_time) as max_ms,
    count as requests
    by transaction_id, layer
| where max_ms > 2000
| sort -max_ms
```
 
---
 
## Skills demonstrated
 
| Area | Detail |
|---|---|
| SPL | Multi-source correlation, subsearches, conditional logic, time-series analysis |
| Dashboard design | Stakeholder-ready layouts, threshold visualization, drill-down navigation |
| Observability | End-to-end transaction tracing, anomaly detection, capacity planning |
| Incident response | MTTR reduction, alert design, triage workflows |
| Data analysis | Cross-layer correlation, pattern recognition, trend projection |
 
---
 
## Repo structure
 
```
📂 01-web-error-rate/
├── README.md          ← Problem, approach, and impact
├── dashboard.json     ← Exportable Splunk dashboard
├── queries.spl        ← Key searches with inline comments
└── screenshots/       ← Visual reference
 
📂 02-infrastructure-health/
└── ...
 
📂 03-transaction-correlation/
└── ...
```
 
---
 
## Contact
 
Open to roles in observability, platform engineering, SRE, or data analytics.
 
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?logo=linkedin)](https://linkedin.com)
[![Email](https://img.shields.io/badge/Email-Contact-D14836?logo=gmail)](mailto:your@email.com)
 
