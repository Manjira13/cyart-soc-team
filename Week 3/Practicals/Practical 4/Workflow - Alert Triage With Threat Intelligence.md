# Alert Triage with Threat Intelligence Workflow

## 1. Overview
- Objective: Simulate alert triage and validate IOCs using threat intelligence.
- Tools: Wazuh (Ubuntu), VirusTotal, AlienVault OTX, Windows PowerShell, SSH client.
- Alert Types: PowerShell execution, SSH login attempts.

## 2. Workflow Steps

### 2.1 Simulate PowerShell Execution Alert
1. On Windows 11, encode the PowerShell command:

$command = 'iex "whoami"'
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
[Convert]::ToBase64String($bytes)
```
2. Execute the encoded command:

powershell.exe -NoP -EncodedCommand <base64_string>
```
3. Wazuh agent captures the event and generates an alert in `/var/ossec/logs/alerts/alerts.log`.
4. Verify the alert in Wazuh:
```bash
sudo tail -f /var/ossec/logs/alerts/alerts.log | grep powershell
```

### 2.2 Simulate SSH Remote Login Alert
1. From Windows, attempt SSH login to Ubuntu:

ssh manjira@192.168.1.35
```
2. Multiple failed login attempts trigger Wazuh alerts.
3. Verify the alert in Wazuh:
```bash
sudo tail -f /var/ossec/logs/alerts/alerts.log | grep ssh
```

### 2.3 Triage Alerts in Wazuh
- Categorize alerts by:
  - Severity
  - Source IP
  - Type of activity
- Use the following command to filter alerts:
```bash
sudo tail -f /var/ossec/logs/alerts/alerts.log
```

### 2.4 Extract Indicators of Compromise (IOCs)
1. Extract public IP addresses (exclude private IPs):

sudo grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' /var/ossec/logs/alerts/alerts.log | \
grep -vE '^10\.|^172\.1[6-9]\.|^172\.2[0-9]\.|^172\.3[0-1]\.|^192\.168\.' | sort -u
```
2. Extract file hashes (if any):

sudo grep -oE '[a-fA-F0-9]{32,64}' /var/ossec/logs/alerts/alerts.log | sort -u > hashes.txt
```

### 2.5 Validate IOCs with Threat Intelligence
1. Cross-reference extracted IPs and hashes with:
   - VirusTotal
   - AlienVault OTX
2. Document the findings (no public IOCs were found in lab environment). 

### 2.6 Document Alert Triage
- Alert Summary Table:
| Alert ID | Description            | Source IP      | Priority | Status |
|----------|------------------------|----------------|----------|--------|
| 004      | PowerShell Execution   | 192.168.1.101  | High     | Open   |
| 005      | SSH Remote Login       | 192.168.1.35   | Medium   | Open   |

## 3. References
- Wazuh Documentation: https://documentation.wazuh.com/
- VirusTotal: https://www.virustotal.com/
- AlienVault OTX: https://otx.alienvault.com/
- PowerShell Docs: https://docs.microsoft.com/en-us/powershell/
- SSH Protocol Overview: https://www.ssh.com/ssh/protocol
