# Alert Triage with Automation Workflow

## Tools Used

* ELK Stack (Elasticsearch & Kibana)
* VirusTotal
* TheHive

## Workflow

### 1. Create and Ingest Mock Alert in ELK

* Prepare a JSON file (`mock_alert.json`) with alert details:

{
  "alert_id": "005",
  "description": "Suspicious File Download",
  "source_ip": "192.168.1.102",
  "priority": "High",
  "status": "Open",
  "file_hash": "e3b0c44298fc1c149afbf4c8996fb924"
}
```

* Ingest the mock alert into Elasticsearch:

curl -k -u elastic:elkpass1 -X POST "https://<ELK-IP>:9200/alerts/_doc/005" \
-H "Content-Type: application/json" \
-d @mock_alert.json
```

* Troubleshoot common issues:

  * **Empty reply from server:** Check if Elasticsearch requires HTTPS and authentication.
  * **Authentication errors:** Ensure the correct API key or username/password is used.
  * **Index not visible in Kibana:** Verify the index pattern is created and matches the alert index.
* Verify ingestion by querying the index:

curl -k -H "Authorization: ApiKey <API_KEY>" -X GET "https://<ELK-IP>:9200/alerts/_search?pretty"
```

### 2. Retrieve File Hash

* Extract the SHA256 hash from the ingested alert.
* If the actual file is available, compute hash using:

sha256sum suspicious_file.exe
```

### 3. Manual Threat Intelligence Validation

* Open VirusTotal ([https://www.virustotal.com](https://www.virustotal.com)).
* Paste the file hash in the search bar.
* Collect key information:

  * Detection ratio
  * File type
  * First seen date
  * Related URLs or files

### 4. Create Case in TheHive

* Login to TheHive Web UI.
* Click **Create Case**.
* Fill in the following details:

  * **Title:** Suspicious File Download from 192.168.1.102
  * **Description:** Alert ID 005 from ELK
  * **Severity:** 3 (High)
  * **Type:** External
  * **TLP:** 2
  * **Artifacts:** Add file hash and VirusTotal summary
* Save the case.
* Troubleshoot connectivity issues:

  * Ensure TheHive can reach Elasticsearch if using automated ingestion.
  * Verify correct API key and certificate configuration.

### 5. Document Findings

* Record the alert details, file hash, VirusTotal results, and TheHive case reference.
* Optionally, summarize in \~50 words for reporting.

### Notes

* Full automation with Cortex and TheHive could not be completed due to Elasticsearch cluster connection issues.
* Manual creation of mock alerts and ingestion into ELK allowed successful simulation of automated triage.
* Troubleshooting included resolving authentication errors, certificate paths, and API key generation.

---

**Alert Table Example:**

| Alert ID | Description   | Source IP     | Priority | Status | File Hash                        | VirusTotal Result |
| -------- | ------------- | ------------- | -------- | ------ | -------------------------------- | ----------------- |
| 005      | File Download | 192.168.1.102 | High     | Open   | e3b0c44298fc1c149afbf4c8996fb924 | 0/70 detections   |
