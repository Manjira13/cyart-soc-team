# Windows Event Log Hunting Workflow

## Tools Used

* Windows 11 Endpoint
* Winlogbeat
* Elasticsearch & Kibana (ELK Stack)
* Velociraptor
* PowerShell

## Workflow

### 1. Generate Windows Event Log Test Events

* Open Command Prompt as a standard user.
* Generate failed login attempts using the `runas` command:

C:\Users\manjira13> runas /user:fakeuser cmd.exe
Enter the password for fakeuser:
Attempting to start cmd.exe as user "MANJIRA\fakeuser" ...
RUNAS ERROR: Unable to run - cmd.exe
1326: The user name or password is incorrect.
```

* Repeat multiple times to create a series of Event ID 4625 (failed logins).
* Generate privileged account usage event (Event ID 4672) using PowerShell:

if (-not [System.Diagnostics.EventLog]::SourceExists("TestVelociraptor")) {
    New-EventLog -LogName Application -Source "TestVelociraptor"
}
Write-EventLog -LogName Application -Source "TestVelociraptor" -EventId 4672 -EntryType Information -Message "Unexpected admin role"
```

* Verify that events appear in the Windows Event Viewer under Application and Security logs.

### 2. Ship Events to Elasticsearch via Winlogbeat

* Confirm Winlogbeat is installed and configured to monitor Security and Application logs.
* Check `C:\ProgramData\Winlogbeat` for registry and metadata files.
* Start Winlogbeat to ensure events are shipped to Elasticsearch.
* Verify ingestion in Elasticsearch using `_search` API or Kibana Discover:

curl -k -u elastic:elkpass1 -X GET "https://192.168.1.39:9200/winlog-test/_search?pretty"
```

* Troubleshoot:

  * Authentication or connection errors: Verify credentials and Elasticsearch endpoint.
  * Missing events: Check Winlogbeat configuration and ensure the service is running.

### 3. Configure Velociraptor Hunt

* Open Velociraptor server interface.
* Create a hunt using the `Windows.EventLogs.Evtx` artifact.
* Set artifact parameters:

  * `EvtxGlob` = `C:\Windows\System32\winevt\Logs\Application.evtx`
  * `IDRegex` = `4672|4625` to capture both privileged account usage and failed login events.
  * Define `StartDate` and `EndDate` to match event timestamps.
* Launch the hunt.
* Troubleshoot:

  * Frontend startup errors: Ensure correct `server.config.yaml` path.
  * Missing results: Enable debug logging and adjust artifact parameters or hunting time window.

### 4. Review and Export Hunt Results

* Export hunt results to CSV from Velociraptor.
* Validate events:

  * Check timestamps, usernames, Event IDs, host and process information, and message content.
* Troubleshoot:

  * No results: Confirm artifact exists and EVTX logs contain targeted events.
  * Adjust IDRegex or hunting window as necessary.

### 5. Example Events

| Timestamp           | User      | Event ID | Message               | Observed in            |
| ------------------- | --------- | -------- | --------------------- | ---------------------- |
| 2025-08-18 15:00:00 | testuser  | 4672     | Unexpected admin role | Velociraptor & Elastic |
| 2025-09-14 02:51:57 | manjira13 | 4625     | Failed logon attempt  | Elastic                |

### 6. Observations and Summary

* Failed login attempts and privileged account events were successfully captured.
* Events were visible in Elasticsearch via Winlogbeat and confirmed in Velociraptor hunts.
* Metadata included user, host, process, Event ID, timestamp, and message content.
* Challenges included correcting credential encoding, configuring artifacts properly, enabling debug logs, and aligning hunt windows with event timestamps.
* A Hunting Report was prepared summarizing the findings, consistent with MITRE ATT\&CK T1078 (Valid Accounts).
