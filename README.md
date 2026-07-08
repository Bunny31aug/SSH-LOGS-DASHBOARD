# SSH-LOGS-DASHBOARD(Splunk)

## Overview
Built a Splunk dashboard to monitor SSH logs — visualizing login attempts, failed connections, and brute-force attacks. Created panels for SSH activity summaries, authentication trends, and geo-location analysis of attack origins, with a shared time-range filter for dynamic data exploration.

## Dashboard Panels
- **SSH Event Summary** — total SSH events, successful logins, failed logins, and connections without authentication
- **Failed Logins by Username** — highlights the most-targeted usernames (e.g., `root`, `admin`, `backup`, `test`)
- **Possible Brute Force by IP Address** — ranks source IPs by attempt count and percentage of total failed logins
- **Brute Force Attack Geo-location** — world map view plotting attack volume by country/region
- **Shared Time Range Filter** — a single time picker drives all panels for consistent, dynamic analysis

## Sample Findings (test dataset)
| Metric | Value |
|---|---|
| Total SSH Events | 2,400 |
| Successful Logins | 612 |
| Failed Logins | 610 |
| Connections without Authentication | 572 |

- Most-targeted usernames: `root`, `backup`, `alice`, `admin`, `test`, `john.doe`, `svc_user`, `service`, `dbadmin`, `webmaster`
- Top offending IPs included `83.195.24.226`, `25.47.52.197`, and `191.47.156.160`, each accounting for ~3–4% of failed login attempts
- Geo-location analysis showed the highest concentration of brute-force attempts originating from the United States, with a secondary cluster in Eastern Europe

## Data Source
- File: `ssh_logs_new.json`
- Host: `Linux_Server`
- Sourcetype: `_json`

## Example SPL Queries

**Total SSH Events**
```spl
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json"
| stats count AS "Total SSH Events"
```

**Successful Logins**
```spl
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Successful SSH Login"
| stats count AS "Successful Logins"
```

**Failed Logins**
```spl
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Failed SSH Login"
| stats count AS "Failed Logins"
```

**Connections Without Authentication**
```spl
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Connection Without Authentication"
| stats count AS "Connection Without Authentication"
```

**Failed Logins by Username**
```spl
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Failed SSH Login"
| top username
```

**Possible Brute Force by IP Address**
```spl
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Multiple Failed Authentication Attempts"
| top id.orig_h
```

**Brute Force Attack Geo-location**
```spl
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Multiple Failed Authentication Attempts"
| table id.orig_h
| iplocation id.orig_h
| stats count by Country
| geom geo_countries featureIdField="Country"
```

All panels share a single time-range input (`time_range`, default last 24 hours) bound via `$time_range.earliest$` / `$time_range.latest$`, so adjusting the time picker updates every panel at once.

## Tech Stack
- Splunk Enterprise
- SPL (Search Processing Language)
- Simple XML (Splunk dashboard definition)
- `iplocation` + `geom` commands for choropleth geo-mapping

## Setup
1. Install Splunk Enterprise (or Splunk Free) locally or in a VM.
2. Ingest SSH/auth log data as `ssh_logs_new.json` (JSON-formatted SSH/Zeek-style events) with sourcetype `_json` on host `Linux_Server` — or update the queries above to match your own source/host/sourcetype.
3. Import `dashboard.xml` into Splunk under **Dashboards > Create New Dashboard > Import** (or paste it into the Source Editor).
4. Use the time-range picker at the top to adjust the analysis window across all panels.

## Repository Contents
- `SSH_Dashboard.xml` — Splunk dashboard source (Simple XML)
- `README.md` — this file

## Author
**Anish Chakraborty**
Security Operations Analyst (SOC)
[LinkedIn](https://linkedin.com/in/anishchakraborty-7944)
