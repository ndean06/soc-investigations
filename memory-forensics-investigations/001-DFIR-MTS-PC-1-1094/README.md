# Fileless Process Injection – Memory Forensics Case  
**Case ID:** MTS-PC-1-1094

---

## Overview
This case documents a **memory-forensics–led DFIR investigation** initiated after suspicious endpoint activity escalated into repeated **process injection alerts**. The investigation focuses on an anomalous instance of `syshost.exe` executing from **PAGE_EXECUTE_READWRITE (RWX)** memory—behavior consistent with **fileless malware, shellcode injection, and reflective loading techniques**.

Disk-based artifacts were limited or absent, requiring a pivot to **volatile memory analysis** to validate malicious execution.

---

## Investigation Request (Initial Context)
> Memory analysis was requested following a high volume of **process injection detections** on host **MTS-PC-1** on **2026-01-23**.  
>  
> The activity was suspected to be related to previously observed malware, a newly executed payload, or both. Due to the severity and fileless nature of the behavior, a **full memory capture** was collected and provided for DFIR analysis.

---

## Key Findings
- Suspicious execution observed within `syshost.exe`
- **RWX memory region** confirmed via VAD analysis
- No reconstructable PE present within RWX VAD (indicative of shellcode or loader code)
- **Memory-backed PE** recovered via ImageSectionObject analysis
- Extracted executable confirmed to exist **only in memory** (not disk-backed)

---

## Technique Classification
Mapped to **MITRE ATT&CK**:
- **T1055 – Process Injection**
- **T1620 – Reflective Code Loading**

---

## Repository Contents
- `walkthrough.md` – Step-by-step investigative workflow and analytical decisions  
- `report.md` – Formal DFIR report and conclusions  
- `artifacts/` – Extracted memory artifacts and hashes  
- `screenshots/` – Evidence supporting investigative steps  

---

## Analyst Notes
This case highlights:
- Memory-first investigation strategies
- Detection of fileless execution tradecraft
- Tooling limitations and investigative pivots
- Practical DFIR workflows used in real SOC environments

---



