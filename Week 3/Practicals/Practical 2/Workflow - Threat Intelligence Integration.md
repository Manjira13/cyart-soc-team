# Threat Intelligence Integration Workflow with Wazuh and AlienVault OTX

## Introduction

This document outlines a step-by-step workflow for integrating threat intelligence into Wazuh using AlienVault OTX. The focus is on importing threat feeds, enriching alerts, and performing threat hunting. Mock alerts are used for demonstration, so real-time IOC matching is not shown.

## Workflow

### 1. Environment Setup

1. Ensure Wazuh manager is installed and running.
2. Verify Wazuh integration directory: `/var/ossec/integrations/`.
3. Place Python scripts for OTX integration in the integrations folder:

   * `custom-alienvault.py`
   * `get_malicious.py`

4. Configure the OTX API key in Wazuh integration settings (`ossec.conf`).

### 2. Importing Threat Feed (Mock)

1. Create a mock alert to simulate an IOC match:

sudo jq -n '{
  id: "003",
  rule: { id: "5502" },
  full_log: "Login attempt from 192.168.1.100",
  agent: { name: "manjira" }
}' | sudo tee /var/ossec/logs/alerts/mock_alert.json > /dev/null
```

2. Run the Python enrichment script on the mock alert:

sudo python3 /var/ossec/integrations/get_malicious.py /var/ossec/logs/alerts/mock_alert.json
```

Output:

```
192.168.1.100: Malicious (OTX)
```

### 3. Alert Enrichment

1. Review Wazuh alerts for the rule ID `5502`:

sudo jq -r 'select(.rule.id=="5502") | .full_log' /var/ossec/logs/alerts/alerts.json | grep -v "system"
```

2. Extract source IPs from the alerts:

sudo jq -r 'select(.rule.id=="5502") | .full_log' /var/ossec/logs/alerts/alerts.json | grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}'
```

### 4. Threat Hunting

1. Filter alerts for suspicious login attempts and gather metadata:

sudo jq -r 'select(.rule.id=="5502") | {alert_id:.id, user:.user.name, src_ip:.srcip}' /var/ossec/logs/alerts/alerts.json
```

2. Analyze patterns to detect T1078 (Valid Accounts) activity.

### 5. Troubleshooting

* If the OTX integration fails:

  * Ensure `custom-alienvault.py` exists and is executable.
  * Confirm the Python environment is working with required modules.
* Use mock alerts if live IOC feeds are unavailable.

### 6. Notes on Real-Time IOC

* Real-time IOC integration is not demonstrated because the alerts used are static/mock.
* No actual IPs from network traffic are present in `/var/ossec/logs/alerts/alerts.json`.
* Live deployment requires continuous IOC feed ingestion.

## Conclusion

This workflow demonstrates:

* Setting up threat feed integration with Wazuh.
* Enriching alerts using OTX data.
* Performing basic threat hunting using mock alerts.

For production environments, live IOC feeds and active alert ingestion are necessary to detect actual threats.

## References

* Wazuh Documentation (https://documentation.wazuh.com/current/index.html)
* AlienVault OTX (https://otx.alienvault.com/)
