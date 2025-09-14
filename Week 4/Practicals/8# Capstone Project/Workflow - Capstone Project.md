# Samba Backdoor Detection Task Workflow

## Task Overview

* **Task Title:** Samba Backdoor Detected
* **Case ID:** \~8294592
* **Assignee:** SOC Analyst
* **Severity:** Medium
* **TLP:** Amber
* **Status:** Completed

## Observables

* Exploit: Metasploit `usermap_script`
* Source IP: 192.168.1.39
* Destination IP: 192.168.1.36
* Protocol: SMB (port 139)
* Zeek Notice: `Samba_Backdoor`
* Detection Source: `/opt/zeek/logs/current/notice.log`

## Step-by-Step Workflow

### 1. Zeek Detection

1. Ensure Zeek is deployed and running:

   ```bash
   sudo /opt/zeek/bin/zeekctl check
   sudo /opt/zeek/bin/zeekctl deploy
   ```
2. Verify Zeek logs for Samba backdoor alerts:

   ```bash
   sudo tail -f /opt/zeek/logs/current/notice.log
   ```
3. Zeek custom script (`samba_backdoor.zeek`) detects `usermap_script` exploit and logs notice type `Samba_Backdoor`.

### 2. Metasploit Verification

1. Launch the Samba exploit:

   ```bash
   msf6 exploit(multi/samba/usermap_script) > set RHOST 192.168.1.36
   msf6 exploit(multi/samba/usermap_script) > set RPORT 139
   msf6 exploit(multi/samba/usermap_script) > exploit
   ```
2. Confirm reverse shell session opens and closes.

### 3. TheHive Case Creation

1. Generate API key for an authorized user with `manageCase/create` permission.
2. Create the case via API:

   ```bash
   curl -s -i -X POST "http://192.168.1.35:9000/api/case" \
        -H "Authorization: Bearer <API_KEY>" \
        -H "Content-Type: application/json" \
        -d '{
             "title": "Samba Backdoor Detected",
             "description": "Metasploit usermap_script exploit detected by Zeek/Wazuh",
             "severity": 2,
             "tlp": 2,
             "tags": ["Samba_Backdoor", "Metasploit", "Zeek"]
           }'
   ```
3. Add observables from TheHive web UI.

** MITRE
** T1210
** Samba_Backdoor

### 4. Containment with CrowdSec

1. Ensure CrowdSec agent is active:

   ```bash
   sudo systemctl enable --now crowdsec
   sudo systemctl status crowdsec
   ```
2. Add IP block manually:

   ```bash
   sudo cscli decisions add --ip 192.168.1.39 --type ban --duration 1h
   sudo cscli decisions list -i 192.168.1.39
   ```
3. Verify the firewall bouncer is operational:

   ```bash
   sudo systemctl enable --now crowdsec-firewall-bouncer
   ```

### 5. Task Completion

1. In TheHive, navigate to the task under the case.
2. Mark the containment task as complete.
3. Note: automated playbook execution is not applicable in this workflow.

### 6. Metrics and Reporting

* Attempted to calculate MTTD and MTTR in Kibana via runtime fields.
* Errors encountered: `Cannot cast from [double] to [void]`.
* Dashboard created using Lens visualization to show timeline and events.
* Metrics calculation omitted due to runtime field limitations.

### 7. Root Cause Analysis (RCA)

* Legacy SMB services exposed on the target host.
* Lack of automated detection enforcement initially.
* Containment performed manually via CrowdSec.

### 8. Recommendations

* Disable legacy SMB scripts and restrict SMB traffic.
* Apply patches and enforce vulnerability management.
* Integrate Zeek, TheHive, and CrowdSec for automated detection and containment.
* Review alternative methods for capturing MTTD and MTTR metrics.

---

