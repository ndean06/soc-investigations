# Fileless Process Injection – Memory Forensics Case

## Overview
This case documents a **memory-forensics–led investigation** that originated from suspicious endpoint activity and escalated into the identification of **fileless malicious execution**. The investigation centers on an anomalous instance of `syshost.exe` executing from **PAGE_EXECUTE_READWRITE (RWX)** memory, consistent with **process injection and reflective code loading**.

The case demonstrates how endpoint context, investigative pivots, and memory analysis were combined to validate in-memory execution **without reliance on disk-based malware artifacts**.

---

## Key Findings
- Suspicious execution identified within `syshost.exe`
- RWX memory region confirmed via VAD analysis
- No reconstructable PE present in RWX VAD (indicative of shellcode or loader code)
- Memory-backed PE recovered through ImageSectionObject analysis
- Extracted executable confirmed to be **in-memory only** and not disk-backed

---

## Technique Classification
- **Process Injection (T1055)**
- **Reflective Code Loading (T1620)**

---

## Contents
- `walkthrough.md` – End-to-end investigation workflow and analytical decisions  
- `report.md` – Formal incident report and conclusions  
- `artifacts/` – Extracted memory artifacts  
- `screenshots/` – Supporting evidence  

---



