# Advanced Log Analysis Workflow

## Introduction

This document details a workflow for performing advanced log analysis using Elastic Security, Security Onion, and Elastic Stack features. The focus is on correlating logs, detecting anomalies, and enriching log data with geolocation information.

## 1. Log Correlation

### Objective

Correlate failed login events (Event ID 4625) with outbound network traffic to detect potential security incidents.

### Steps

1. **Simulate Failed Logins on Windows**

   * Use `runas` command to simulate failed login attempts.

     C:\Users\manjira13>runas /user:FakeUser cmd
     Enter the password for FakeUser:
     RUNAS ERROR: Unable to run - cmd
     1326: The user name or password is incorrect.
     ```
   * Repeat using lock screen and RDP login attempts.

2. **Collect Windows Security Events**

   Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4625 -or $_.Id -eq 4624} |
   Select-Object TimeCreated, Id, @{Name='SourceIP';Expression={($_.Properties[18].Value)}}, Message |
   Format-Table -AutoSize
   ```

3. **Export to CSV**

   * Use PowerShell to export table to CSV while maintaining structure.

     Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4625 -or $_.Id -eq 4624} |
     Select-Object TimeCreated, Id, @{Name='SourceIP';Expression={($_.Properties[18].Value)}}, Message |
     Export-Csv -Path SecurityEvents.csv -NoTypeInformation
     ```

4. **Ingest Logs into Elastic Security**

   * Install Winlogbeat on the Windows host.
   * Configure Winlogbeat to ship security logs to Elasticsearch.

## 2. Anomaly Detection

### Objective

Detect high-volume data transfers using an Elastic rule.

### Steps

1. **Ingest Sample Network Logs**

   * Example log:

     POST logs-test/_doc
     {
       "@timestamp": "2025-09-02T12:15:00Z",
       "source.ip": "192.168.1.39",
       "destination.ip": "8.8.8.8",
       "network.bytes_out": 2500000
     }
     ```

2. **Create Detection Rule in Elastic Security**

   * Rule: `network.bytes_out > 1MB in 1 minute`
   * Test rule by sending mock file transfer logs.

3. **Verify Alerts**

   * Check Elastic Security alerts panel for triggered rule.

## 3. Log Enrichment with GeoIP

### Objective

Enhance logs with geolocation data to identify IP origin.

### Steps

1. **Create GeoIP Ingest Pipeline**

   ```json
   PUT _ingest/pipeline/geoip_pipeline
   {
     "description": "Add GeoIP info",
     "processors": [
       {
         "geoip": {
           "field": "source.ip",
           "target_field": "source.geo",
           "database_file": "GeoLite2-City.mmdb"
         }
       }
     ]
   }
   ```

2. **Ingest Logs Through Pipeline**

   ```json
   POST logs-test/_doc?pipeline=geoip_pipeline
   {
     "@timestamp": "2025-09-02T12:15:00Z",
     "source.ip": "8.8.8.8",
     "destination.ip": "192.168.1.39",
     "network.bytes_out": 2500000
   }
   ```

3. **Verify Enrichment**

   * Check that documents now include `source.geo` fields (city, country, latitude, longitude).

4. **Summarize Findings**

   * Use Kibana Maps or CSV export to show geolocation of public IPs involved in traffic.
   * Example summary (50 words):

     > The enriched logs reveal outbound traffic from 192.168.1.39 to public IP 8.8.8.8, which is geolocated to Mountain View, California, United States. GeoIP enrichment aids in identifying unusual network destinations and assists in threat investigation by providing context to IP addresses in logs.

## Conclusion

By following this workflow, security analysts can:

* Correlate failed login attempts with outbound network activity.
* Detect anomalous high-volume data transfers.
* Enrich logs with geolocation data to enhance visibility.

Next steps include automating log ingestion, refining detection rules, and integrating enriched data into dashboards for continuous monitoring.

## References

* Elastic Security Documentation https://www.elastic.co/guide/en/security/current/index.html
* Winlogbeat Documentation https://www.elastic.co/guide/en/beats/winlogbeat/current/index.html
* Elastic GeoIP Processor https://www.elastic.co/guide/en/elasticsearch/reference/current/geoip-processor.html
* GeoLite2 Database: MaxMind https://dev.maxmind.com/geoip/geolite2-free-geolocation-data
