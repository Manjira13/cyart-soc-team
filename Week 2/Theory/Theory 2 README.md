# Incident Classification – Detailed Overview

## 1. Core Concepts

### 1.1 Incident Categories
Incidents are security events that could compromise **confidentiality, integrity, or availability**.  
Proper classification is critical for efficient response. Common categories include:
 
| Category                                 | Definition                                                               | Example                                                                  |
|------------------------------------------|--------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Malware                                  | Software designed to disrupt, damage, or gain unauthorized access        | Ransomware encrypting corporate files, Trojan downloading sensitive data |
| Phishing                                 | Fraudulent attempts to obtain sensitive information via email, messages, | Employee receiving a spoofed email requesting login credentials          |
|                                          | or websites                                                              |                                                                          |
| DDoS (Distributed Denial of Service)     | Attack to overwhelm systems or networks, causing service unavailability  | Botnet flooding a web server with requests, causing downtime             |
| Insider Threat                           | Malicious or negligent actions by internal personnel                     | Employee exporting confidential customer data without authorization      |
| Data Exfiltration                        | Unauthorized transfer of sensitive information outside the organization  | Sensitive database records sent to an external email or cloud account    |
| Unauthorized Access / Account Compromise | Use of stolen or misused credentials                                     | Attacker logging in as a privileged user to modify system files          |
| Policy Violation / Misconfiguration      | Breach of internal security policies or misconfigured systems            | Open S3 buckets exposing confidential data, use of insecure protocols    |


---

### 1.2 Taxonomy / Standardized Frameworks
Using **standard frameworks** ensures consistency in labeling incidents across teams and organizations.

- **MITRE ATT&CK**  
  - Structured knowledge base of tactics and techniques used by adversaries.  
  - Example: `T1566 – Phishing` → Maps all phishing-related incidents to this technique.  
  - Supports linking incidents to attack patterns for **threat intelligence and detection tuning**.  

- **ENISA Incident Taxonomy**  
  - Classifies incidents into broad categories (cybercrime, cyber espionage, operational incidents).  
  - Provides a consistent language for reporting and comparing incidents across organizations.  

- **VERIS (Vocabulary for Event Recording and Incident Sharing)**  
  - Framework for recording incident details, including actor, action, asset, impact, and timeline.  
  - Encourages enrichment with contextual metadata (e.g., source IP, timestamps, malicious hashes).  


---

### 1.3 Contextual Metadata
To make incident classification actionable, each incident should be enriched with metadata:

| Metadata Type                   | Description                                     | Example                                             |
|---------------------------------|-------------------------------------------------|-----------------------------------------------------|
| Affected Systems / Assets       | Devices, servers, or applications impacted      | Corporate email server, HR database                 |
| Timestamps                      | Time of detection, occurrence, and containment  | 2025-08-20 09:30 UTC                                |
| Source IP / Location            | Origin of attack or suspicious activity         | 192.168.1.45 (internal), 203.0.113.25 (external)    |
| Indicators of Compromise (IOCs) | Evidence linking to malicious activity          | Malicious hash, phishing URL, suspicious file names |
| Actor / Threat Group            | Known or suspected attacker                     | FIN7, unknown insider                               |
| Impact Assessment               | Confidentiality, integrity, availability impact | Data theft, system downtime                         |

Adding metadata allows for:
- Better **incident correlation**  
- Faster **root cause analysis**  
- Improved **reporting and investigations**  

**References:**
---

## 2. Key Objectives
- Categorize incidents consistently using **standard frameworks**.  
- Label incidents with accurate taxonomy, tactic, and technique.  
- Enrich incidents with **contextual metadata** for investigation and reporting.  
- Improve SOC efficiency by enabling **prioritization, correlation, and analysis**.  

---

## 3. Study Approach

### Explore MITRE ATT&CK
- Map incidents to **tactics and techniques**.  
  - Example:  
    - Phishing campaign → `T1566`  
    - Ransomware → `T1486`  

### Study ENISA & VERIS Frameworks
- Learn categories and structure for **standard classification**.  
- Practice labeling incidents using **ENISA broad categories** and **VERIS fields**.  

### Review Case Studies
- Use [SANS Reading Room] (https://www.sans.org/white-papers) or **CISA incident reports** for real-world examples.  
- Practice enriching incidents with timestamps, affected systems, IOCs, and impact assessment.  

### SOC Tool Simulation
- Use **SIEM tools** (Splunk, Wazuh, Elastic) to classify and tag mock incidents.  
- Observe how classification affects **alert prioritization and response workflow**.  

**References:**
- MITRE ATT&CK (https://attack.mitre.org)  
- ENISA Incident Classification (https://www.enisa.europa.eu/topics/csirt-cert-services/incident-classification)  
- VERIS (https://veriscommunity.net)  
- SANS Reading Room Case Studies (https://www.sans.org/white-papers)  
- SANS Reading Room – Incident Handling Case Studies (https://www.sans.org/white-papers)  
- NIST SP 800-61 Rev. 2 (Incident Documentation) (https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)  


---
