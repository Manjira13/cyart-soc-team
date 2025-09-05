# Capstone Project: Full SOC Workflow Simulation

## Introduction

This document provides a comprehensive workflow for simulating a full SOC operation, including attack simulation, detection, triage, response, escalation, and reporting. The task leverages tools such as Metasploit, Wazuh, CrowdSec, TheHive, and Google Docs.

## Workflow

### 1. Attack Simulation

Objective: Exploit a vulnerability in Metasploitable2 using Metasploit.

Steps:

1. Launch Metasploit on Kali VM.
2. Search and select Samba usermap script exploit: `use exploit/multi/samba/usermap_script`.
3. Set the target IP: `set RHOST 192.168.1.39`.
4. Execute the exploit: `run`.
5. Confirm the exploit outcome and note potential indicators of compromise (IOCs).

### 2. Detection and Triage

Objective: Configure Wazuh to detect the attack and log relevant alerts.

Steps:

1. Add custom Wazuh rule in `/var/ossec/etc/rules/local_rules.xml` to detect the Samba exploit.
2. Configure localfile monitoring in `/var/ossec/etc/ossec.conf` for `/var/log/syslog` and `/var/log/auth.log`.
3. Restart Wazuh manager: `sudo systemctl restart wazuh-manager`.
4. Verify alert generation using `logger` or appending exploit logs to monitored files.

Alert Documentation:

| Timestamp           | Source IP    | Alert Description | MITRE Technique |
| ------------------- | ------------ | ----------------- | --------------- |
| 2025-09-05 17:04:49 | 192.168.1.39 | Samba exploit     | T1210           |

### 3. Response and Containment

Objective: Isolate affected VM and mitigate the attack.

Steps:

1. Block attacker IP using CrowdSec.
2. Isolate Metasploitable2 VM from network.
3. Verify network isolation with ping tests.
4. Document actions taken for audit.

### 4. Escalation

Objective: Escalate the incident to Tier 2 analysts.

Escalation Summary:

Wazuh detected a potential security incident originating from IP 192.168.1.39 (Kali VM) targeting Metasploitable2 via a Samba exploit. The alert was captured in `/var/log/syslog` and `/var/log/auth.log`, but no escalation-level alerts were triggered due to ingestion delays in Elasticsearch. Tier 2 analysts should review the incident, validate rules, and assess alert thresholds. Immediate containment included isolating the VM and blocking the attacker's IP.

### 5. Reporting

Objective: Document the incident for stakeholders.

Steps:

1. Write a 200-word report in Google Docs using a SANS template.

   * Include Executive Summary, Timeline, and Recommendations.
2. Draft a 100-word briefing for non-technical managers summarizing the incident and actions taken.

### 6. Troubleshooting

* Issue: Alerts not appearing in Wazuh.

  * Check Wazuh manager status: `sudo systemctl status wazuh-manager`.
  * Verify localfile paths in `ossec.conf`.
  * Ensure Elasticsearch is running and ingesting logs properly.

* Issue: Elasticsearch errors.

  * Stop and restart Elasticsearch:

    ```
    sudo systemctl stop elasticsearch
    sudo pkill -f elasticsearch
    sudo systemctl start elasticsearch
    ```
  * Check `/var/log/elasticsearch/` for logs.

### 7. Observations

* Exploits were executed and Wazuh rules were configured correctly.
* Logs were not captured initially due to Elasticsearch ingestion and indexing delays.
* After verifying log paths and restarting services, alerts should appear once ingestion stabilizes.

## Conclusion

This workflow demonstrates a full SOC simulation, from attack to reporting. Key points:

* Accurate rule creation in Wazuh is critical.
* Elasticsearch stability is required for proper log ingestion.
* Containment and escalation processes must be followed for effective incident response.

## References

* Metasploit Unleashed: https://www.offensive-security.com/metasploit-unleashed/
* Wazuh Documentation: https://documentation.wazuh.com/
* CrowdSec Documentation: https://doc.crowdsec.net/
* TheHive Project: https://thehive-project.org/
* SANS Incident Reporting Templates: https://www.sans.org/information-security-training/
