\# Alert Priority Levels – Detailed Overview



\## 1. Core Concepts



\### 1.1 Priority

Alert priority defines how urgently a SOC (Security Operations Center) team must respond to a security event.  

It is based on two main factors: \*\*impact\*\* and \*\*urgency\*\*.



| Priority  | Definition                                                        | Example                                                                                 |

|-----------|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|

| Critical  | Immediate action required; high impact \& high urgency             | Ransomware actively encrypting production servers; active data exfiltration             |

| High      | Major impact, prompt attention required; less urgent than Critical| Unauthorized admin access, confirmed intrusion attempts, CVEs with active exploits      |

| Medium    | Moderate impact; action required in normal SOC workflow           | Suspicious activity without confirmed compromise, outdated software without exploit     |

| Low       | Minimal impact; informational; may be monitored                   | Login attempts from unusual locations without evidence of compromise, policy violations |



\#### Key Considerations

\- \*\*Asset Criticality\*\*: Production servers, databases, or core business applications have higher priority than test VMs or lab environments.  

\- \*\*Exploit Likelihood\*\*: Known vulnerabilities with public exploits (e.g., CVEs listed on NVD or Exploit-DB) are higher priority than theoretical vulnerabilities.  

\- \*\*Business Impact\*\*: Includes financial loss, reputational damage, or regulatory fines.  



\#### Example

\- CVSS score of \*\*9.8\*\* for Log4Shell (CVE-2021-44228) → \*\*Critical\*\* because it affects internet-facing servers with public exploits.  

\- A single failed login attempt on a test VM → \*\*Low/Medium\*\* depending on context.  





---



\### 1.2 Scoring Systems



\#### CVSS (Common Vulnerability Scoring System)

Provides standardized scoring for vulnerabilities. Scores range \*\*0–10\*\*, with higher scores indicating more severe vulnerabilities.  



\- \*\*Base Metrics\*\*: Exploitability (attack vector, complexity, privileges required) and impact (confidentiality, integrity, availability).  

\- \*\*Temporal Metrics\*\*: Adjusted based on exploit maturity, availability of fixes, or reports of active exploitation.  

\- \*\*Environmental Metrics\*\*: Tailored to organizational context (e.g., internal server vs. public-facing web server).  



\#### SOC Tool Risk Scoring

Tools like \*\*Splunk, QRadar, or Wazuh\*\* can automatically assign risk scores to alerts based on CVSS, source IP reputation, or other heuristics.  

This helps prioritize alerts without requiring manual evaluation of every event.  



\*\*Example:\*\*

\- Log4Shell CVSS Base Score = \*\*10 → Critical\*\*  

\- SMBv1 exploit on isolated test network = \*\*Medium\*\* (high base score but low business impact)  



\*\*References:\*\*



---



\## 2. Practical Prioritization



When completing assignments or real SOC tasks, consider three main factors:



1\. \*\*Asset Criticality\*\*  

&nbsp;  - Production database servers > Development/Test VMs > Personal endpoints  

&nbsp;  - Example:  

&nbsp;    - Ransomware on production DB = \*\*Critical\*\*  

&nbsp;    - Same on lab VM = \*\*Medium\*\*  



2\. \*\*Exploit Likelihood\*\*  

&nbsp;  - Public exploit available or active attack campaign → \*\*Higher priority\*\*  

&nbsp;  - CVE without exploit → \*\*Lower priority\*\*  



3\. \*\*Business Impact\*\*  

&nbsp;  - Financial loss, downtime, or regulatory impact increases priority  

&nbsp;  - Example: Log4Shell on customer-facing webserver = \*\*Critical\*\* due to potential data exposure  



\#### Mapping CVSS Score to Priority Level



| CVSS Score | Likely Alert Priority | Notes                                              |

|------------|-----------------------|----------------------------------------------------|

| 9–10       | Critical              | Exploitable remotely, high impact                  |

| 7–8.9      | High                  | High impact, but requires some conditions          |

| 4–6.9      | Medium                | Moderate risk, limited impact                      |

| 0–3.9      | Low                   | Minor risk, informational                          |



&nbsp;



---



\## 3. How to Learn / Study Approach



\### CVSS Study

\- Read \[FIRST CVSS Guide](https://www.first.org/cvss/specification-document)  

\- Practice scoring recent vulnerabilities:  

&nbsp; - \*\*Log4Shell (CVE-2021-44228) → 10 (Critical)\*\*  

&nbsp; - \*\*PrintNightmare (CVE-2021-34527) → 8.8 (High)\*\*  



\### Incident Handling Guidance

\- Read \[NIST SP 800-61 Rev. 2](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) to understand alert severity, workflows, and escalation paths.  

\- Practice mapping sample alerts to priority levels.  



\### Real-World Case Analysis

\- Review \[CISA Alerts](https://www.cisa.gov/uscert/ncas) and \[MITRE ATT\&CK](https://attack.mitre.org) techniques.  

\- Analyze \*\*CVSS score → priority mapping → incident handling decisions\*\*.  



\### SOC Tools Practice

\- Use \*\*Splunk, Wazuh, or Elastic SIEM\*\* to observe automated alert prioritization.  

\- Study how risk scores influence response workflow.  



\*\*References:\*\*

\- NIST SP 800-61 Revision 2 (https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

\- FIRST CVSS v3.1 Specification (https://www.first.org/cvss/specification-document)

\- CVSS v3.1 User Guide (https://www.first.org/cvss/specification-document)

\- Splunk Enterprise Security Risk Scoring (https://docs.splunk.com/Documentation/ES)

\- NVD CVE Database (https://nvd.nist.gov)

\- CISA Alerts \& Advisories (https://www.cisa.gov/uscert/ncas)

\- MITRE ATT\&CK Framework (https://attack.mitre.org)  

\- CISA Alerts \& Advisorie (https://www.cisa.gov/uscert/ncas)  



---



