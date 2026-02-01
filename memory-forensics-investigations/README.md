# Memory Forensics Investigations

## Purpose
This folder contains investigations focused on **memory forensics and volatile evidence analysis**. The cases here examine suspicious or malicious activity that is **not fully observable through disk artifacts alone**, including **in-memory execution**, **process injection**, and **fileless techniques**.

These investigations are intended to demonstrate **how memory analysis is used in practice**, not to showcase a single tool or methodology.

---

## What Belongs in This Folder
Investigations in this folder typically involve:
- Analysis of Windows memory images
- Identification of suspicious process behavior
- Examination of Virtual Address Descriptors (VADs)
- Detection of RWX (Read/Write/Execute) memory regions
- In-memory PE, shellcode, or reflective loader analysis
- Extraction and validation of volatile artifacts

---

## Tools & Approaches
Multiple tools may be used depending on the case, including but not limited to:
- Volatility 3
- Native OS utilities
- PE analysis tools
- Custom workflows for address-aware extraction

The emphasis is on **investigative reasoning and decision-making**, rather than reliance on any single tool.

---

## How to Use These Investigations
Each investigation is self-contained and can be reviewed independently.

A typical case includes:
- `README.md` – Brief case overview and scope  
- `walkthrough.md` – Step-by-step investigation process and rationale  
- `report.md` – Formal incident report and conclusions  
- `artifacts/` – Extracted memory artifacts  
- `screenshots/` – Supporting evidence and command output  

Readers are encouraged to start with the case `README.md`, then proceed to the walkthrough and report for deeper detail.

---

> _This folder focuses on understanding attacker behavior in memory and demonstrating how analysts adapt when traditional artifacts are unavailable._
