# Basic Incident Response

## 1. Incidents

### 1.1 Incident Lifecycle
The **incident lifecycle** represents the structured phases a SOC or security team follows to handle security incidents efficiently.  
Understanding each phase ensures proper containment, recovery, and prevention of future incidents.

| Phase          | Description                                             | Examples / Activities                                                                     |
|----------------|---------------------------------------------------------|----------------------------------------------------------------------------------------|
| Preparation    | Establish policies, procedures, and tools for incident  | Develop playbooks, configure monitoring systems, train staff, ensure backups           |
|                | response                                                |                                                                                        |
| Identification | Detect and confirm security incidents                   | Alert triage, log analysis, intrusion detection systems (IDS), user reports            |
| Containment    | Limit the scope and impact of the incident              | Isolate affected systems, disable compromised accounts, network segmentation           |
| Eradication    | Remove threats and vulnerabilities from the environment | Remove malware, patch exploited systems, eliminate unauthorized access                 |
| Recovery       | Restore systems to normal operations safely             | Restore services from clean backups, validate system integrity, monitor for recurrence |
| Lessons Learned| Analyze the incident to prevent recurrence              | Post-mortem meetings, root cause analysis, update playbooks, report to management      |


---

### 1.2 Procedures and Best Practices
To respond effectively, SOC teams follow standardized procedures during incident handling:

#### System Isolation
- Disconnect compromised systems from the network to prevent lateral movement.  
- **Examples:** VLAN segmentation, firewall rules, physically unplugging systems if needed.  

#### Evidence Preservation
- Maintain integrity of data for investigation and legal purposes.  
- **Techniques:** Memory dumps, file hashing (MD5/SHA256), disk imaging, log collection.  
- Avoid altering or overwriting evidence during containment and eradication.  

#### Communication Protocols
- Notify relevant stakeholders based on severity and business impact.  
- Follow predefined **communication templates** for internal teams, management, or regulatory reporting.  

#### SOAR Tools (Security Orchestration, Automation, and Response)
- Automate repetitive response actions for faster mitigation.  
- **Examples:** Splunk Phantom, Demisto (Cortex XSOAR), Wazuh automation scripts.  
- Can automate tasks like alert triage, system isolation, or ticket creation.  
 

---

## 2. Key Objectives
- Understand the **Incident Lifecycle**: Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned  
- Implement **Procedures**: System isolation, evidence preservation, communication, and automated response.  
- Develop **Readiness**: Respond efficiently to various incident types and minimize business impact.  

---

## 3. Study Approach

### Study NIST SP 800-61 Rev. 2
- Focus on lifecycle phases, response procedures, and incident documentation standards.  
- Learn **when and how to escalate incidents**.  

### Use SANS Incident Handler’s Handbook
- Study templates for incident reporting, communication, and containment checklists.  
- Practice documenting **mock incidents** for preparation and lessons learned phases.  

### Simulated Incident Response
- Platforms like [Let’s Defend](https://letsdefend.io) provide hands-on scenarios to practice IR skills.  
- Simulate ransomware, phishing, or insider threat incidents to apply lifecycle concepts.  

### SOAR Tool Exploration
- Use trial or free versions of SOAR tools (e.g., **Splunk Phantom Community Edition**).  
- Practice creating **playbooks** for repetitive incident response tasks.  

**References:**
- NIST SP 800-61 Rev. 2 (https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)  
- SANS Incident Handler’s Handbook (https://www.sans.org/white-papers/1741/)  
- NIST SP 800-61 Rev. 2 – Sections 3–5 (https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)  
- SANS Incident Handler’s Handbook – Evidence Collection & Analysis (https://www.sans.org/white-papers/1741/)  
- SOAR Overview – Gartner Research (https://www.gartner.com/en/documents/3989056) 
- Let’s Defend (https://letsdefend.io)  
- NIST SP 800-61 Rev. 2 (https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)  
- SANS Incident Handler’s Handbook (https://www.sans.org/white-papers/1741/)  

---

## ✅ Summary Key Points
- The **incident lifecycle** ensures structured, efficient response: Preparation → Identification → Containment → Eradication → Recovery → Lessons Learned.  
- Follow best practices: **system isolation, evidence preservation, communication protocols, automation via SOAR tools**.  
- Build readiness through **frameworks, case studies, and simulations**.  
- Practicing with **real-world tools** (Wazuh, Splunk Phantom, etc.) strengthens IR capabilities.  
