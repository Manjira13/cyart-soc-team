# Phishing Response Playbook Development Workflow

## Objective

Simulate a simplified SOAR playbook for phishing alert response, focusing on verifying and blocking a malicious IP without automated tools like Splunk Phantom or Cortex.

## Tools Used

* TheHive (for alert/case management)
* CrowdSec (for manual IP blocking)
* VirusTotal / AlienVault OTX (for threat intelligence)

## Workflow Steps

### 1. Ingest Alert in TheHive

* **Action:** Manually ingest a phishing alert via JSON into TheHive.
* **Details:** Alert contains artifacts such as IP (`192.168.1.102`) and URL (`http://malicious.example.com`).
* **Procedure:**

  1. Prepare JSON alert payload with title, description, type, source, artifacts, and severity.
  2. Execute cURL command to POST the JSON to TheHive API:


  curl -X POST http://localhost:9000/api/alert \
    -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer <API_KEY>' \
    -d @alert.json
  ```

  3. Verify alert creation in TheHive Web UI (status `New`).
* **Outcome:** Alert created successfully in TheHive and ready to be promoted.

### 2. Promote Alert to Case in TheHive

* **Task Name:** Promote Alert
* **Action:** Convert the ingested alert into a TheHive case for incident tracking.
* **Procedure:**

  1. Open TheHive Web UI and navigate to Alerts.
  2. Select the phishing alert.
  3. Click "Promote to Case" and fill in relevant fields (title, description, severity, tags).
  4. Confirm creation of case with associated artifacts.
* **Outcome:** Case created in TheHive for tracking investigation and mitigation steps.

### 3. Check IP Reputation (Manual)

* **Task Name:** Check IP Reputation
* **Action:** Verify if the IP is malicious using VirusTotal and AlienVault OTX.
* **Steps:**

  1. Go to VirusTotal and AlienVault OTX websites.
  2. Enter the IP `192.168.1.102` and check threat intelligence reports.
  3. Document the findings in TheHive case notes.
* **Outcome:** IP confirmed as malicious.

### 4. Block Malicious IP (Manual)

* **Task Name:** Block IP
* **Action:** Manually block the confirmed malicious IP using CrowdSec.
* **Steps:**

  1. Run command:

  sudo cscli decisions add --ip 192.168.1.102 --duration 1h --reason "Phishing alert"
  ```

  2. Verify the block:

  sudo cscli decisions list | grep 192.168.1.102
  ```
* **Outcome:** IP successfully blocked.

## Playbook Table

| Playbook Step           | Status  | Notes                                                            |
| ----------------------- | ------- | ---------------------------------------------------------------- |
| Ingest Alert in TheHive | Success | Alert ingested via JSON API and visible in TheHive UI            |
| Promote Alert to Case   | Success | Alert promoted to case in TheHive for investigation              |
| Check IP                | Success | IP `192.168.1.102` verified as malicious via VirusTotal/OTX      |
| Block IP                | Success | IP `192.168.1.102` manually blocked using CrowdSec (`cscli ban`) |

## Summary

This simplified playbook demonstrates manual phishing alert response. Alerts were ingested and promoted in TheHive for proper tracking. The suspicious IP was verified using external threat intelligence and subsequently blocked via CrowdSec to prevent further malicious activity. This workflow showcases effective incident handling without automated SOAR tools.
