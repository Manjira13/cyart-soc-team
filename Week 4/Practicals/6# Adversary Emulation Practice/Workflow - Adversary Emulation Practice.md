# Adversary Emulation Practice Workflow



## 1. Start Caldera Server

* Navigate to the Caldera installation directory.
* Run the server:


python3 server.py --insecure --build
```

* Verify that the Caldera Web UI is accessible.

## 2. Create Adversary Profile

* Use the Caldera Web UI to define a custom adversary profile.
* Include test commands labeled `CALDERA_TEST_ENTRY` for logging purposes.

## 3. Deploy Agent (Manual Step)

* **Note:** The Caldera Web UI did not have the option to deploy an agent.
* Manually commands were run on the host machine.

## 4. Execute Emulation

* Trigger the adversary against the deployed agent.
* Generate test log entries:

```bash
echo "CALDERA_TEST_ENTRY $(date)" | sudo tee -a /home/manjira/caldera/logs/caldera.log
```

* Verify that commands execute successfully and logs are generated.

## 5. Configure Filebeat

* Edit `/etc/filebeat/filebeat.yml` to include a filestream input for Caldera logs:

```yaml
filebeat.inputs:
  - type: filestream
    id: caldera-logs
    enabled: true
    paths:
      - /home/manjira/caldera/logs/caldera.log
    pipeline: caldera-pipeline
    tags: ["caldera_test"]
    fields:
      source: caldera
    fields_under_root: true
    scan_frequency: 10s
    start_position: beginning
    ignore_older: 0s
    multiline:
      pattern: '^CALDERA_TEST_ENTRY'
      match: after
```

* Restart Filebeat:

```bash
sudo systemctl restart filebeat
```

* Troubleshoot ingestion issues (e.g., small file size <1 KB).

## 6. Zeek Log Integration

* Append entries to Zeek log file:

```bash
echo "CALDERA_TEST_ENTRY $(date)" | sudo tee -a /opt/zeek/logs/current/caldera_test.log
```

* Configure Filebeat filestream input for Zeek log if required.

## 7. Verify Logs in Elasticsearch

* Query Elasticsearch to confirm logs are ingested:

```bash
curl -u elastic:elkpass1 -s "https://192.168.1.39:9200/filebeat-*/_search?q=CALDERA_TEST_ENTRY&pretty" --insecure
```

* Check that new log entries appear.

## 8. Outcome

* Emulation was successful; adversary executed against the agent.
* Filebeat successfully ingested logs after addressing small file issue.

## Notes

* Small log files must exceed 1 KB for Filebeat filestream input.
* Filebeat registry may require clearing to re-harvest files.
* Zeek logs are a reliable backup for capturing events.
