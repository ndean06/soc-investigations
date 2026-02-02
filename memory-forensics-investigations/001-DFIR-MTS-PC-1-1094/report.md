# Incident Report – Fileless Process Injection via Memory Forensics

---

## 1. Report Title
**Fileless Process Injection via In-Memory Execution on MTS-PC-1**

---

## 2. Date of Report
**2026-01-29**

---

## 3. Reported By
**Nigel D – SOC / DFIR Analyst**

---

## 4. Severity Level
**High**

---

## 5. Summary of Findings

An investigation into suspicious endpoint activity on **MTS-PC-1** revealed **fileless malicious execution** occurring entirely in memory. The process `syshost.exe` (PID 10272) exhibited behavior consistent with **process injection**, including **RWX memory allocation** and **manual PE mapping**. No complete disk-backed malware payload was identified, indicating deliberate evasion of file-based detection mechanisms.

---

## 6. Investigation Timeline

| Time (UTC) | Event Description |
|-----------|------------------|
| 2026-01-23 17:22 | Microsoft Defender for Endpoint generated alerts for suspicious process injection |
| 2026-01-23 17:24 | `syshost.exe` identified as anomalous process |
| 2026-01-23 17:30 | Incident escalated due to repeated injection indicators |
| 2026-01-23 17:45 | Full memory capture acquired from MTS-PC-1 |
| 2026-01-29 | Memory image analyzed using Volatility 3 |
| 2026-01-29 | RWX memory region identified via VAD analysis |
| 2026-01-29 | In-memory PE artifact extracted and analyzed |

---

## 7. Who, What, When, Where, Why, How

**Who**  
- User: `MTS\Zach.Balrog`  
- System: `MTS-PC-1`

**What**  
- Fileless process injection and in-memory execution involving `syshost.exe`

**When**  
- Initial activity observed on **2026-01-23**

**Where**  
- Endpoint: **MTS-PC-1**

**Why**  
- Likely post-exploitation activity designed to evade disk-based detection and maintain stealthy execution

**How**  
- Injection of shellcode into a userland process  
- Allocation of **PAGE_EXECUTE_READWRITE (RWX)** memory  
- Manual mapping of a PE image into memory without writing it to disk

---

## 8. MITRE ATT&CK Techniques

- **T1055 – Process Injection**
- **T1620 – Reflective Code Loading**
- **T1106 – Native API Usage**

---

## 9. Impact Assessment

- No confirmed data exfiltration observed  
- Elevated risk of credential theft, persistence, or lateral movement if activity had continued  
- Fileless execution significantly reduced visibility for traditional antivirus and file-based monitoring

---

## 10. Recommendations / Next Steps

- Reimage affected endpoint to ensure full remediation  
- Review EDR policies for enhanced detection of RWX memory behavior  
- Monitor environment for similar process injection patterns  
- Hunt for additional indicators related to reflective loading or manual PE mapping

---

## 11. Attachments / Evidence

- Memory image (`primary.raw`)  
- Process memory dumps (`pid.10272.dmp`)  
- Extracted ImageSectionObject artifacts  
- PE hash values (SHA-256)  
- Microsoft Defender for Endpoint screenshots  
- Investigation walkthrough (`walkthrough.md`)

---

## 12. Report Status
**Closed**

---

## 13. Reviewed By

**SOC Lead / DFIR Reviewer**  
Date: ____________________
