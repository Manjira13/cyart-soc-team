# Incident Escalation Practice – Unauthorized Access on Server-Y

## Introduction

This document outlines the incident escalation practice exercise carried out using TheHive for case and alert management, and Google Docs for reporting. The exercise simulated a high-priority alert involving unauthorized access to a server. It covered case creation, alert ingestion via API, initial triage, escalation to Tier 2, drafting a Situation Report (SITREP), and workflow automation using a simulated Splunk Phantom (SOAR) playbook.

## 1. Overview

* Tools Used: TheHive, cURL, Google Docs, Python (mock automation)
* Scenario: Simulated unauthorized access to Server-Y from IP 192.168.1.200
* MITRE Technique: T1078 (Valid Accounts)
* Severity: High (3)
* Goal: Demonstrate the escalation process from Tier 1 triage to Tier 2 handoff, SITREP documentation, and workflow automation.

## 2. Step-by-Step Guide

### 2.1 Case Creation in TheHive

* Logged into TheHive web interface.
* Manually created a new case titled: "Unauthorized Access on Server-Y."
* Case served as a container for documentation and escalation simulation.
* Note: Case was not directly linked to alert (mock simulation allowed treating them separately).

### 2.2 Alert Ingestion via API

* Created JSON file `unauthorized_mockalert.json`:

{
  "title": "Unauthorized Access on Server-Y",
  "description": "Detected unauthorized login attempt on Server-Y from IP 192.168.1.200",
  "severity": 3,
  "tags": ["Unauthorized Access", "MITRE T1078"],
  "artifacts": [
    {"dataType": "ip", "data": "192.168.1.39", "message": "Observed source IP"},
    {"dataType": "hostname", "data": "Server-Y", "message": "Affected host"},
    {"dataType": "user", "data": "admin", "message": "Compromised account"}
  ],
  "source": "SimulatedAlert",
  "sourceRef": "sim-alert-001"
}
```

* Ingested via cURL:

curl -X POST http://192.168.1.35:9000/api/alert \
-H "Authorization: Bearer <API_KEY>" \
-H "Content-Type: application/json" \
-d @unauthorized_mockalert.json
```

* Alert confirmed in TheHive with status New and artifacts attached.

### 2.3 Initial Triage (Tier 1 Actions)

* Reviewed artifacts: IP 192.168.1.39, Host Server-Y, User admin.
* Simulated containment: Server-Y isolated from the network.
* Reviewed authentication and firewall logs.
* Added notes in alert documenting findings and containment actions.

### 2.4 Escalation to Tier 2

* Prepared a 100-word escalation summary:

```
A high-priority alert was triggered for unauthorized access on Server-Y, detected at 2025-08-18 13:00 from IP 192.168.1.39 (MITRE T1078 – Valid Accounts). Initial triage was performed by Tier 1: Server-Y was isolated from the network to prevent lateral movement, authentication and firewall logs were reviewed, and artifacts including the source IP, affected hostname, and compromised user account were documented. Tier 2 is requested to conduct a detailed forensic analysis, assess potential credential compromise, check for malware or backdoors, and implement remediation measures. All findings should be logged in the alert for further review.
```

### 2.5 SITREP Draft in Google Docs

* **Title:** Unauthorized Access on Server-Y
* **Summary:** Detected at 2025-09-03 01:31, IP: 192.168.1.39, MITRE T1078
* **Actions:** Isolated server, escalated to Tier 2
* Included sections: Incident Overview, Triage Actions, Escalation Summary, Recommended Next Steps, Notes.

### 2.6 Workflow Automation (Simulated Splunk Phantom Playbook)

* Due to installation issues with Splunk SOAR, a Python script was used to simulate a playbook.
* **Python Script (`phantom_playbook_auto_assign.py`):**

alert = {
    "title": "Unauthorized Access on Server-Y",
    "severity": 3
}

tier_assigned = "Tier2"

print(f"Alert '{alert['title']}' assigned to {tier_assigned}.")
```

* **Execution:**

python3 phantom_playbook_auto_assign.py
# Output: Alert 'Unauthorized Access on Server-Y' assigned to Tier2.
```

* Reason for Using Python Script Instead of Real Playbook:

  * Splunk SOAR environment could not be installed due to insufficient disk space and systemd errors.
  * Automation Broker CLI could not be used due to Docker image access restrictions and package installation issues.
  * Python script serves as a mock to demonstrate the workflow logic.

## 3. Troubleshooting Notes

* Could not link alert to case in TheHive; treated them as related but independent.
* Alert ingestion required JSON adjustments: severity as integer, artifacts instead of observables, sourceRef mandatory.
* SOAR installation failed due to disk space, daemon reload, and connectivity warnings.

## 4. Conclusion / Summary

* Case created to document the incident.
* Alert ingested via API with relevant artifacts.
* Tier 1 triage and containment completed.
* 100-word escalation summary drafted and added.
* SITREP prepared in Google Docs.
* Python-based workflow automation simulated the auto-assignment of high-priority alerts to Tier 2.
* Future implementation could integrate a full SOAR environment for real playbook execution.

## References

* TheHive Project Documentation: https://docs.strangebee.com
* MITRE ATT\&CK Technique T1078 – Valid Accounts: https://attack.mitre.org/techniques/T1078/
* Splunk SOAR (Phantom): https://www.splunk.com/en_us/software/soar.html
