# Evidence Analysis Workflow: Velociraptor CSV with FTK Imager

## Introduction
This workflow describes how to take a CSV file generated from Velociraptor (e.g., `SELECT * FROM netstat` query results), process it through FTK Imager, and maintain a proper chain-of-custody with hash verification.

---

## Tools Used
- Velociraptor: For running queries and exporting results as CSV.
- FTK Imager: For creating forensic images of the CSV evidence and verifying integrity.

---

## Step-by-Step Workflow

### 1. Export Evidence from Velociraptor
1. Run the query in Velociraptor, e.g.:

   SELECT * FROM netstat
   ```
2. Export the results as a CSV file (e.g., `netstat_results.csv`).

---

### 2. Open FTK Imager
1. Launch FTK Imager on your analysis machine (Windows VM).

---

### 3. Add CSV as Evidence Item
1. Go to File → Create Disk Image.  
2. Choose Contents of a Folder / Files.  
3. Select the CSV file (`netstat_results.csv`).  

---

### 4. Configure Image Destination
1. Choose a destination folder (e.g., external flash drive).  
2. Select image type**:  
   - `E01` (EnCase format, supports metadata and compression)  
   - or `Raw (dd)` (bit-by-bit copy).  
3. Ensure hash calculation is enabled (MD5, SHA1, SHA256).  

---

### 5. Create the Image
1. Review the summary.  
2. Click Start to create the forensic image.  
3. Wait until the process completes.  

---

### 6. Verify Evidence Integrity
1. Go to File → Verify Evidence Item.  
2. Select the newly created image.  
3. Confirm that the calculated hash values **match** the original.  

---

## Chain-of-Custody Documentation
Record details of collected evidence, including the CSV forensic image:

| Item              | Description               | Collected By | Date       | Hash Value        |
|-------------------|---------------------------|--------------|------------|-------------------|
| Network Log (CSV) | netstat_results.csv image | SOC Analyst  | 2025-08-18 | 47CB0EFAAFC222AD  |
|                   |                           |              |            |  B3A6B047DD976C3D |
|                   |                           |              |            | 49AB8BCFD9532874  |
|                   |                           |              |            | 3A3B24515F9987F4  |

---

## Conclusion
- The CSV file from Velociraptor is preserved as a **forensic image** using FTK Imager.  
- Hash values ensure integrity and are logged in the chain-of-custody.  
- The original CSV remains unchanged, while the forensic copy is stored securely for investigation.
