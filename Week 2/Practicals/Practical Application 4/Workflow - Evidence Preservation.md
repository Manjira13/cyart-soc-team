# Evidence Preservation Workflow using Velociraptor

## Introduction
This document provides a structured workflow for evidence preservation using **Velociraptor**. The process includes setting up Velociraptor, running live forensic queries (`netstat` for active connections and memory acquisition), preserving the acquired evidence, and generating SHA256 hashes for verification. This ensures that evidence is collected and preserved in a forensically sound manner.

---

## Overview
The workflow covers:
- Setting up Velociraptor on Windows.
- Running forensic queries from the Velociraptor notebook.
- Collecting active network connection details.
- Acquiring a memory image of the system.
- Preserving evidence in a secure location.
- Generating hash values for integrity verification.

---

## Step-by-Step Workflow

### 1. Setup Velociraptor
1. Download Velociraptor from the [official GitHub repository](https://github.com/Velocidex/velociraptor).
2. Extract it to a local folder (e.g., `C:\Velociraptor`).
3. Launch Velociraptor:
      powershell
   cd C:\Velociraptor
   .\velociraptor.exe gui
4. Open the Velociraptor notebook in your browser (https://localhost:8889).

---

### 2. Run Netstat Query (Active Connections)
1. Open a new cell in the Velociraptor notebook.

2. Run the following query to capture active network connections:

SELECT * FROM netstat()

3. Save the results for documentation and later analysis.

---

### 4. Analyze Live Network Connections with Netstat  
1. Open PowerShell as Administrator.  
2. Run Netstat to list all current network connections and listening ports:  
      powershell
   netstat -ano
      
3. Example output:  
      
   Proto  Local Address          Foreign Address        State           PID
   TCP    192.168.1.5:49210      93.184.216.34:443      ESTABLISHED     1234
   TCP    192.168.1.5:49211      142.250.186.14:443     ESTABLISHED     5678
   ```
   - **Proto**: Protocol (TCP/UDP).  
   - **Local Address**: Local system IP and port.  
   - **Foreign Address**: Remote IP and port.  
   - **State**: Connection status (e.g., LISTENING, ESTABLISHED).  
   - **PID**: Process ID associated with the connection.  

---

### 4 Memory Acquisition Query
1. In Velociraptor notebook, create another cell.

Run the memory acquisition query:

   SELECT * FROM plugins.memory.AcquireMemory(
    output="C:\\Velociraptor\\artifacts\\PhysicalMemory.dd"
)
3. This generates a raw memory dump file at:

   C:\Velociraptor\artifacts\PhysicalMemory.dd

---

### 5. Preserve the Evidence
1. Copy the memory dump (PhysicalMemory.dd) and netstat results to a secure storage location (e.g., external drive or forensic evidence folder).

2. Once the copy is verified, you may delete older or duplicate dumps from the local system to save space.

3. Ensure only the required copy is retained for integrity.

---

### 6. Verify Integrity using Hashing
1. Open PowerShell and run:

   certutil -hashfile "C:\Velociraptor\artifacts\PhysicalMemory.dd" SHA256
Output:

SHA256 hash of C:\Velociraptor\artifacts\PhysicalMemory.dd:
60bcd51654822894beabf516ed78a799e26d82a61289f6724f6ae8e13213bcc3
CertUtil: -hashfile command completed successfully.

3. Save the hash value alongside the evidence file for chain-of-custody documentation.

### Troubleshooting
- Large Dump Size (5GB+): Use an external drive or compressed archive if disk space is limited.

- Permission Errors: Ensure Velociraptor is run with administrator privileges.

- Hash Mismatch: Re-copy the file and verify again to ensure no corruption occurred.

---

### Conclusion
By following this workflow:

- Active network connections were documented using netstat.

- A complete memory dump was acquired via Velociraptor.

- Evidence was securely preserved and verified using SHA256 hashing.

- This ensures the evidence is collected, preserved, and validated in a forensically sound manner.

---

### References
- Velociraptor Official GitHub: https://github.com/Velocidex/velociraptor

- Microsoft CertUtil Documentation: https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/certutil

- Netstat Command Guide: https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/netstat  
