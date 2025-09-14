# Threat Hunting Methodologies  

## Introduction  
Threat hunting is the proactive process of searching for hidden threats in an organization’s network before they cause damage. Unlike reactive incident response, which begins after an alert is triggered, threat hunting assumes compromise and looks for evidence of malicious activity. By leveraging structured methodologies, attacker TTPs (Tactics, Techniques, and Procedures), and multiple data sources, analysts can uncover sophisticated attacks that evade traditional security tools.  

---

## Core Concepts  

### 1. Proactive Threat Hunting  
Threat hunting relies on **hypothesis-driven searches** rather than waiting for alerts. Analysts form a hypothesis based on attacker behaviors (e.g., MITRE ATT&CK techniques) and test it against available data.  

Example:
- **Hypothesis:** An attacker is using **MITRE ATT&CK T1078 – Valid Accounts** to access systems with compromised credentials.  
- **Hunting Method:** Search authentication logs for unusual privilege escalations or logins from unexpected locations.  

| Hunting Type      | Example                                         | Benefit                                      |
|-------------------|-------------------------------------------------|----------------------------------------------|
| Hypothesis-driven | Hunt for anomalous privilege escalation in logs | Proactive identification of stealthy attacks |
| Reactive          | Investigation triggered after SIEM alert        | Confirms or refutes existing alerts          |

---

### 2. Hunting Frameworks  
Frameworks provide structured methodologies for conducting hunts effectively.  

**a) SqRR (Search, Query, Retrieve, Respond)**  
- Search: Define hypothesis and search across data sources.  
- Query: Build queries to detect suspicious behavior.  
- Retrieve: Gather relevant evidence.  
- Respond: Escalate or remediate based on findings.  

**b) TaHiTI (Targeted Hunting integrating Threat Intelligence)**  
- Integrates threat intelligence feeds with hunting hypotheses.  
- Uses known IOCs and TTPs to guide focused hunts.  

| Framework | Steps                                               | Key Benefit                                     |
|-----------|-----------------------------------------------------|-------------------------------------------------|
| SqRR      | Search → Query → Retrieve → Respond                 | Clear workflow for hypothesis-driven hunts      |
| TaHiTI    | Intelligence → Hypothesis → Data Query → Validation | Integrates threat intelligence for guided hunts |

---

### 3. Data Sources for Hunting  
Effective threat hunting requires **diverse data sources** to uncover attacker activity.  

| Data Source                              | Example Use                                            | Hunting Value                              |
|------------------------------------------|--------------------------------------------------------|--------------------------------------------|
| Endpoint Detection & Response (EDR) Logs | Detect persistence techniques (e.g., registry changes) | Endpoint-level visibility                  |
| Network Traffic                          | Spot lateral movement or data exfiltration             | Identifies abnormal communication patterns |
| Threat Intelligence Feeds                | Known IOCs, domains, C2 servers                        | Guides targeted hunts                      |
| SIEM Data                                | Correlation of authentication + firewall logs          | Centralized hunting queries                |

---

## Key Objectives  
By mastering threat hunting methodologies, you should be able to:  
- Form **hypotheses** based on attacker TTPs.  
- Apply structured frameworks like SqRR and TaHiTI.  
- Leverage multiple data sources (logs, EDR, network, intel) for hunts.  
- Proactively detect stealthy threats that evade standard security controls.  

---

## References  
1. SANS Institute. *Threat Hunting Whitepapers.* Available at: [https://www.sans.org/white-papers/](https://www.sans.org/white-papers/)  
2. MITRE ATT&CK. *Adversary Tactics and Techniques.* Available at: [https://attack.mitre.org/](https://attack.mitre.org/)  
3. Elastic Security Labs. *Threat Hunting Guide.* Available at: [https://www.elastic.co/security-labs](https://www.elastic.co/security-labs)  
4. MITRE. *APT29 Case Studies and Emulation Plans.* Available at: [https://attack.mitre.org/resources/adversary-emulation-plans/](https://attack.mitre.org/resources/adversary-emulation-plans/)  

---
