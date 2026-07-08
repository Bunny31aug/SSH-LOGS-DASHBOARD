# SSH-LOGS-DASHBOARD
# SSH Logs Dashboard (Splunk)

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

## Tech Stack
- Splunk Enterprise
- SPL (Search Processing Language)
- SSH/auth log data (e.g., Zeek/Bro `ssh.log` or Linux `auth.log`)

## Setup
1. Install Splunk Enterprise (or Splunk Free) locally or in a VM.
2. Ingest SSH authentication logs into a Splunk index (via a forwarder, syslog input, or file monitor).
3. Import the dashboard definition (`dashboard.xml` — add to this repo) into Splunk under **Dashboards > Create New Dashboard > Import**.
4. Adjust the shared time-range filter and index/source names in the SPL queries to match your environment.

## Repository Contents
- `dashboard.xml` — Splunk dashboard source (Simple XML)
- `screenshots/` — sample dashboard views (event summary, brute-force table, geo-location map)
- `README.md` — this file

## SPL Queries Used
1.	Total SSH Events: 
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json"
 | stats count AS "Total SSH Events"	

2.	Successful Logins:
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Successful SSH Login" 
| stats count AS "Successful Logins"

3.	Failed SSH Login:
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Failed SSH Login" 
| stats count AS "Successful Logins"

4.	Connection Without Authentication:
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Connection Without Authentication" 
| stats count AS "Successful Logins"

5.	Failed Logins By Username:
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Failed SSH Login" | top username

6.	Possible Brute Force by IP Address:
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Multiple Failed Authentication Attempts" | top id.orig_h

7.	Brute Force attack with geo-location:
source="ssh_logs_new.json" host="Linux_Server" sourcetype="_json" event_type="Multiple Failed Authentication Attempts"  | table id.orig_h | iplocation id.orig_h
| stats count by Country
| geom geo_countries featureIdField="Country"


## Author
**Anish Chakraborty**
Security Operations Analyst (SOC)
[LinkedIn](https://linkedin.com/in/anishchakraborty-7944)
