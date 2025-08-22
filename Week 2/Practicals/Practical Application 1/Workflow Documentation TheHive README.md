# Workflow Documentation: TheHive Alerts → Wazuh Dashboard → Incident Ticket

## Introduction
This document provides a step-by-step workflow to integrate TheHive alerts with Wazuh Dashboards for visualization and incident management. It covers creating mock alerts in TheHive, exporting them, visualizing in Wazuh, and drafting incident tickets for escalation.

---

## Step 1: Start TheHive
1. Open terminal.
2. Ensure TheHive Docker container is running:
  
  docker start thehive
  
3. Access TheHive web interface:
   - URL: `http://<host_ip>:9000`
   - Login using admin account.

---

## Step 2: Import/Create Alerts in TheHive
1. Prepare mock alerts in JSON format (`alert1.json`, `alert2.json`, `alert3.json`).
2. Import alerts using TheHive API:

   curl -X POST -H "Content-Type: application/json" \
        -H "Authorization: Bearer <API_key>" \
        -d @"$HOME/alert1.json" \
        http://<thehive_host>:9000/api/alert
   
   curl -X POST -H "Content-Type: application/json" \
        -H "Authorization: Bearer <API_key>" \
        -d @"$HOME/alert2.json" \
        http://<thehive_host>:9000/api/alert

   curl -X POST -H "Content-Type: application/json" \
        -H "Authorization: Bearer <API_key>" \
        -d @"$HOME/alert3.json" \
        http://<thehive_host>:9000/api/alert


3. Verify alerts in **TheHive → Alerts tab** with correct priority, type, and source.

---

## Step 3: Export Alerts from TheHive
1. Navigate to **Alerts tab**.
2. Select alerts → **Export** as **CSV** 
3. Save file locally (e.g., `thehive_alerts.csv`).

---

## Step 4: Open Wazuh Dashboards
1. In browser, go to Wazuh Dashboards:
   - URL: `http://<wazuh_host>:5601`
2. Login using Wazuh credentials.

---

## Step 5: Create a Custom Dashboard
1. Go to **Dashboard → Create Dashboard**.
2. Name: **TheHive Alert Priorities**.

---

## Step 6: Add Visualizations
1. Click **Add Visualization → Pie Chart (or Bar Chart)**.
2. Configure:
   - **Data Source:** Upload `thehive_alerts.csv`.
   - **Metric:** Count of alerts.
   - **Buckets / Split By:** Priority (Critical, High, Medium, Low).
   - Optionally add **Type** as secondary split.
3. Save and add visualization to dashboard.

---

## Step 7: Review Dashboard
1. Confirm alerts appear correctly:
   - Critical → Ransomware
   - Medium → Phishing
   - Low → Network scan
2. Adjust chart settings (colors, labels) for clarity.

---

## Step 8: Draft an Incident Ticket in TheHive
1. Go to **Cases → Create Case**.
2. Fill details:
   - **Title:** `[Critical] Ransomware Detected on Server-X`
   - **Description:** Indicators: [File: crypto_locker.exe],
   - **Priority:** Critical
   - **Assignee:** SOC Analyst
3. Save case.

---

## Step 9: Escalation Role-Play
Draft escalation email to Tier 2 SOC analysts:

**Subject:** Critical Alert Escalation – Ransomware Detected

**Body:**
Dear Tier 2 Team,

A critical ransomware incident has been detected on Server-X. Indicators include file `crypto_locker.exe` and suspicious IP `192.168.1.50`. Immediate containment and investigation are required. TheHive case [Case ID] has been created with relevant artifacts attached. Please prioritize analysis, confirm isolation of affected systems, and provide remediation updates.

Regards,  
SOC Analyst

---

## Step 10: Documentation & Reporting
1. Capture screenshots of:
   - TheHive Alerts
   - Wazuh Dashboard
2. Include in assignment report.
3. Ensure steps (alert creation, visualization, ticket creation, escalation) are documented chronologically.