# Wazuh Log Collection and AlienVault OTX Integration Workflow

## Introduction
This document provides a detailed step-by-step workflow for setting up **Wazuh** to collect logs from an endpoint, verify their presence, and integrate with **AlienVault OTX (Open Threat Exchange)** for threat intelligence correlation.  
The process covers installation, configuration, verification, troubleshooting, and OTX alert validation.  

---

## Overview
- **Goal:** Collect logs from endpoints using Wazuh and enrich them with AlienVault OTX threat intelligence.  
- **Key Steps:**  
  1. Install and verify Wazuh Manager & Dashboard.  
  2. Connect an endpoint (Windows/Linux) as a Wazuh Agent.  
  3. Verify logs from the endpoint are being collected.  
  4. Configure and enable AlienVault OTX integration.  
  5. Confirm that OTX intelligence is applied to logs and alerts are generated.  

---

## Step-by-Step Workflow

### 1. Wazuh Manager and Dashboard Setup
1. Ensure Wazuh Manager and Dashboard are installed and running.  
   ```bash
   sudo systemctl status wazuh-manager
   sudo systemctl status wazuh-dashboard

2. Access the dashboard in a browser:

   https://<wazuh_server_ip>

3. Log in using your Wazuh admin credentials.

---

### 2. Agent Installation and Connection

1. On Windows Endpoint
- Download and install the Wazuh Agent.

- During installation, configure:
  --Wazuh Manager IP → <https://192.168.1.35>

  --Agent name → e.g., Win11

- Start the Wazuh Agent service from services.msc.

2. On Linux Endpoint
- Install the Wazuh Agent package.

- Configure the agent:

   sudo vi /var/ossec/etc/ossec.conf

- Start the agent:

   sudo systemctl enable wazuh-agent
   sudo systemctl start wazuh-agent
3. Verify Agent Registration
- On the Wazuh Manager:

   /var/ossec/bin/agent_control -l
- See the endpoint listed as active.

---

### 3. Log Verification
1. Navigate to the Wazuh Dashboard → Agents → Agent Name → Security Events.

2. Confirm that events/logs are being collected (e.g., Windows Event Logs, Sysmon, or Linux auth logs).

3. Logs are stored under:

   Linux: /var/ossec/logs/

   Windows: %PROGRAMFILES%\ossec-agent\logs

---

### 4. Windows 11 (client)**
   - **OpenSSH Client** installed (usually present):
        powershell
     Get-WindowsCapability -Online | ? Name -like 'OpenSSH.Client*'
    
     # Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
     
   - (Optional) Wazuh Agent installed and healthy (for Windows event visibility; not required for SSH logs).

3. **Network**
   - Windows can reach Ubuntu on TCP/22 (ensure VM/host firewall allows it).

---

### 5. Generate SSH Brute‑Force Attempts from Windows 11
1. **Find the Ubuntu IP** (on Ubuntu):
      bash
   ip a  # note the IPv4, e.g., 192.168.1.10
   
2. **From Windows 11 (PowerShell or CMD)**, attempt logins with a **non‑existent user** (triggers Wazuh rule `5710`: “Attempt to login using a non‑existent user”). You will be prompted for a password—press **Enter** or type any wrong password and press **Enter** once.
      cmd
   ssh invaliduser@192.168.1.35 
   ssh invaliduser@192.168.1.35 
   ssh invaliduser@192.168.1.35 
   ssh invaliduser@192.168.1.35 
   ssh invaliduser@192.168.1.35 
   

   **Tip (looped attempts, manual password):**
      powershell
   $ip="192.168.1.35"
   1..5 | ForEach-Object { ssh invaliduser@$ip }
   
   Since SSH does not read passwords from stdin for security, you’ll still confirm the prompt once per attempt.

---

### 6. Verify on Ubuntu (Ground Truth)
On Ubuntu, confirm the attempts landed in the auth log:
```bash
sudo tail -f /var/log/auth.log

Typical lines:

Invalid user invaliduser from 192.168.1.37 port 50001
Failed password for invalid user invaliduser from 192.168.1.37 port 50001 ssh2
Connection closed by invalid user invaliduser 192.168.1.37 port 50001 [preauth]

> Windows IP (e.g., `192.168.1.37`) should appear as `data.srcip` in Wazuh.

---

### 7. View & Filter Alerts in Wazuh
1. Open **Wazuh Dashboard → Security Events** (or Discover).
2. Useful filters (adapt to your environment):
   - By rule: rule.id:5710
   - By decoder: decoder.name:sshd
   - By source IP: data.srcip:"192.168.1.37" (Windows client IP)
   - By program: predecoder.program_name:sshd
3. Example fields you should see:
   - rule.description: sshd: Attempt to login using a non-existent user
   - rule.level: 5
   - data.srcip: <Windows_IP>
   - data.dstuser: invaliduser
   - full_log: ... port <random_port> [preauth]

---

### 8. AlienVault OTX Integration
1. Get an OTX API key from AlienVault OTX.

- Sign up / log in.

2. Enter the source IP from your alerts into the search bar.
3. Analyze the results:

- If the IP is listed, note associated threat categories (e.g., brute-force, botnet).
- If the IP is not listed, mark as no known threat.
- False Positive
---

### Troubleshooting

- No agent logs showing

- Check connectivity:

   ping <wazuh_manager_ip>

- Verify agent status:

   systemctl status wazuh-agent

---

### Conclusion

- Installed and verified Wazuh Manager & Dashboard.

- Registered Windows/Linux endpoints as agents.

- Confirmed log collection from agents.

- Integrated AlienVault OTX threat intelligence.

- Validated that OTX enriches logs and generates alerts.

- This setup ensures proactive detection of threats based on global IOCs shared via AlienVault OTX.

### References

- https://documentation.wazuh.com/current/index.html
- https://otx.alienvault.com?utm_source=chatgpt.com