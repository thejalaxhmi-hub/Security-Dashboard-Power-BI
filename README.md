# 📊 Security Dashboard — Power BI

> **AI-powered Power BI dashboards for SOC KPI monitoring, log trend visualisation & automated security reporting**

![PowerBI](https://img.shields.io/badge/Power_BI-F2C94C?style=flat&logo=powerbi&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Data_Pipelines-4479A1?style=flat&logo=mysql&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-Data_Processing-150458?style=flat&logo=pandas&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-1D9E75?style=flat)
![Internship](https://img.shields.io/badge/Techolas_Technologies-Data_Science_Intern-557C94?style=flat)

---

## 📌 Overview

A collection of **AI-powered Power BI dashboard templates** built for security operations monitoring, developed during a **12-month Data Science Internship at Techolas Technologies Pvt Ltd** (Dec 2024 – Dec 2025). These dashboards bridge the gap between raw security log data and actionable operational intelligence — giving SOC teams and security managers a clear, real-time view of their environment.

Each dashboard template is backed by **Python data pipelines** (pandas, NumPy) and **SQL queries** for data extraction, cleaning, and transformation before visualisation in Power BI.

---

## 🎯 Problem Statement

SOC teams generate enormous volumes of alert and log data daily. Without proper visualisation, patterns are missed, KPIs go untracked, and reporting becomes a manual burden. These dashboards automate that process:

- Surface **security KPIs** at a glance for analysts and managers
- Visualise **log trends** to identify anomalies before they escalate
- Automate **weekly/monthly reports** — eliminating manual data pulls
- Apply **ML models** to flag unusual patterns in security metrics

---

## 📁 Repository Structure

```
security-dashboard-powerbi/
│
├── dashboards/
│   ├── soc-kpi-dashboard.pbix          # SOC KPI monitoring dashboard
│   ├── alert-trend-dashboard.pbix       # Alert volume & trend analysis
│   ├── log-analysis-dashboard.pbix      # Log source breakdown & anomalies
│   └── incident-report-dashboard.pbix   # IR metrics & resolution times
│
├── data-pipeline/
│   ├── extract.py                       # SQL data extraction scripts
│   ├── transform.py                     # pandas cleaning & transformation
│   ├── load.py                          # Load to Power BI data model
│   └── pipeline.py                      # Full ETL orchestration
│
├── sql-queries/
│   ├── alert-summary.sql                # Daily alert counts by severity
│   ├── top-threat-sources.sql           # Top attacking IPs/domains
│   ├── mttr-calculation.sql             # Mean Time To Resolve queries
│   └── kpi-metrics.sql                  # KPI aggregation queries
│
├── ml-models/
│   ├── anomaly-detection.py             # Isolation Forest on alert data
│   └── trend-forecasting.py            # Alert volume forecasting
│
├── screenshots/
│   └── dashboard-previews/              # Dashboard screenshots
│
├── requirements.txt
└── README.md
```

---

## 📊 Dashboard Templates

### 1. SOC KPI Dashboard

Monitors the core metrics every SOC manager needs daily.

```
┌─────────────┬─────────────┬─────────────┬─────────────┐
│  Total      │  Critical   │  MTTD       │  MTTR       │
│  Alerts     │  Open       │  Avg        │  Avg        │
│  247        │  12         │  4.2 min    │  38 min     │
└─────────────┴─────────────┴─────────────┴─────────────┘

Charts:
• Alert volume by hour (bar chart)
• Severity breakdown (donut chart)
• Alert source distribution (treemap)
• Weekly trend with forecast line
```

**Key KPIs tracked:**

| Metric | Description |
|---|---|
| Total Alerts | Count of all alerts in time period |
| Critical Open | Unresolved critical severity alerts |
| MTTD | Mean Time To Detect (alert trigger to analyst) |
| MTTR | Mean Time To Resolve (detection to closure) |
| False Positive Rate | % of alerts closed as false positives |
| Escalation Rate | % of alerts escalated to Tier 2/3 |

---

### 2. Alert Trend Dashboard

Visualises alert patterns over time to identify anomalies and predict peaks.

```
Features:
• 30-day rolling alert volume
• Day-of-week heatmap (when do most alerts fire?)
• Alert spike detection (Z-score based)
• ML forecast — next 7-day alert volume prediction
• Rule-by-rule breakdown (which detection rules fire most?)
```

---

### 3. Log Analysis Dashboard

Breaks down log sources, volumes, and anomalies across the environment.

```
Features:
• Log ingestion volume by source (firewall, IDS, endpoint)
• Log gap detection (missing sources alert)
• Top event IDs by frequency
• Anomalous log volume (Isolation Forest flags)
• Geographic IP map of inbound connections
```

---

### 4. Incident Report Dashboard

Tracks incident lifecycle metrics and team performance.

```
Features:
• Open vs. closed incidents over time
• Average resolution time by severity
• Incident type breakdown (phishing, malware, insider, etc.)
• Analyst workload distribution
• Monthly incident summary — auto-generated report export
```

---

## ⚙️ Data Pipeline

### ETL Flow

```
Raw Security Data (logs, alerts, DB)
          │
          ▼
┌─────────────────────┐
│   SQL Extraction     │  ← query SIEM DB or log aggregator
└─────────────────────┘
          │
          ▼
┌─────────────────────┐
│   Python Transform   │  ← pandas: clean, normalise, enrich
└─────────────────────┘
          │
          ▼
┌─────────────────────┐
│   Anomaly Detection  │  ← scikit-learn Isolation Forest
└─────────────────────┘
          │
          ▼
┌─────────────────────┐
│   Power BI Model     │  ← load to .pbix data model
└─────────────────────┘
          │
          ▼
┌─────────────────────┐
│   Dashboard + Report │  ← visualise + auto-export PDF
└─────────────────────┘
```

### Sample SQL Query — Daily Alert Summary

```sql
SELECT
    DATE(alert_timestamp)     AS alert_date,
    severity,
    COUNT(*)                  AS alert_count,
    COUNT(CASE WHEN status = 'false_positive' THEN 1 END) AS fp_count,
    COUNT(CASE WHEN status = 'resolved' THEN 1 END)       AS resolved_count,
    AVG(TIMESTAMPDIFF(MINUTE, alert_timestamp, resolved_timestamp)) AS avg_mttr_min
FROM
    security_alerts
WHERE
    alert_timestamp >= DATE_SUB(NOW(), INTERVAL 30 DAY)
GROUP BY
    DATE(alert_timestamp), severity
ORDER BY
    alert_date DESC, severity;
```

### Sample Python Transform

```python
import pandas as pd
import numpy as np
from sklearn.ensemble import IsolationForest

# Load raw alert data
df = pd.read_csv("alerts_raw.csv", parse_dates=["timestamp"])

# Clean & normalise
df.dropna(subset=["severity", "source_ip"], inplace=True)
df["severity"] = df["severity"].str.upper().str.strip()
df["hour"] = df["timestamp"].dt.hour
df["day_of_week"] = df["timestamp"].dt.day_name()

# Daily alert counts
daily = df.groupby(df["timestamp"].dt.date).size().reset_index()
daily.columns = ["date", "alert_count"]

# Anomaly detection on daily volumes
iso = IsolationForest(contamination=0.05, random_state=42)
daily["anomaly"] = iso.fit_predict(daily[["alert_count"]])
daily["is_anomaly"] = daily["anomaly"].apply(lambda x: "Yes" if x == -1 else "No")

# Export for Power BI
daily.to_csv("alerts_processed.csv", index=False)
print(f"Processed {len(daily)} days | {daily['is_anomaly'].value_counts()['Yes']} anomalies flagged")
```

---

## 🤖 ML Features

### Anomaly Detection — Isolation Forest

Flags days where alert volume is statistically abnormal — could indicate an attack wave, detection rule failure, or data pipeline issue.

```python
# Contamination = 5% → flags top 5% unusual days
IsolationForest(contamination=0.05)
```

### Alert Volume Forecasting

Uses a simple rolling average + trend decomposition to forecast next 7 days of alert volume — helps with SOC staffing decisions.

---

## 🔗 Forensics & Security Relevance

Power BI dashboards directly support security operations by:

- **Situational awareness** — SOC analysts see the full picture at a glance
- **Evidence documentation** — dashboard exports serve as timestamped evidence records
- **Pattern detection** — visualising trends surfaces what raw logs hide
- **Compliance reporting** — automated reports support audit requirements
- **Incident retrospectives** — MTTR/MTTD trends expose response gaps

Skills demonstrated: `data engineering` · `security analytics` · `Python automation` · `SQL` · `ML for anomaly detection` · `Power BI`

---

## 🎓 Internship Context

**Data Science Intern — Techolas Technologies Pvt Ltd**
Dec 2024 – Dec 2025 (12 months)

Responsibilities:
- Built AI-powered Power BI dashboards to monitor KPIs
- Wrote Python automation scripts (pandas, NumPy) for data pipelines
- Applied scikit-learn ML models to real business data problems
- Maintained documentation for pipelines, model outputs, and reports

---

## 👩‍💻 Author

**Thejalakshmi AK**
Cyber Forensics Graduate | Data Science Intern | SOC Analyst Trainee

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat&logo=linkedin)](https://linkedin.com/in/theja-lakshmi-1b4447362)
[![Email](https://img.shields.io/badge/Email-Contact-D14836?style=flat&logo=gmail&logoColor=white)](mailto:thejalaxhmi@gmail.com)
[![GitHub](https://img.shields.io/badge/GitHub-thejalakshmi-181717?style=flat&logo=github)](https://github.com/thejalakshmi)

---

## 📄 License

Open source under the [MIT License](LICENSE).

---

<p align="center">Built with 📊 data-driven security thinking · Kerala, India</p>
