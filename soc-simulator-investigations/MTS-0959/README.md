# SOC Incident Investigation – MTS-0959

## Incident Description

This repository documents a **simulated SOC incident investigation** completed for practice and skill development.  
In this scenario, an attacker used **stolen credentials** to access systems, move between machines, run malware, and create a malicious email rule.

The purpose of this investigation is to practice:
- Alert triage
- Timeline building
- Scoping
- Writing clear, client-facing reports

---

## Incident Overview

- **Incident ID:** MTS-0959  
- **Date:** December 23, 2025  
- **Incident Type:** Credential-based attack  
- **Status:** Confirmed and contained  

---

## What Happened (High Level)

1. **Initial Access**
   - The attacker logged into a Windows system using a compromised administrator account over RDP.

2. **Lateral Movement**
   - The attacker used RDP to access another Windows system with a different admin account.

3. **Malware Execution**
   - **Win32/Neshta.A** malware ran on a Windows system and infected multiple files while pretending to be legitimate OneDrive-related processes.

4. **Linux Server Access**
   - The attacker logged into a Linux server as root using SSH.
   - They ran basic discovery commands and downloaded a script from an external server.

5. **Email Activity**
   - A malicious Outlook inbox rule was created to hide emails containing sensitive keywords.
   - This behavior matches common **Business Email Compromise (BEC)** activity.

---

## Scope (What Was Affected)

**Windows Systems**
- `mts-contractorpc1`
- `desktop-1`

**Linux System**
- `MTS-Web`

**Cloud Mailbox**
- `Zach Balrog`

**Scope Result:**  
Attacker activity was limited to the systems and mailbox listed above. No additional systems or accounts were affected.

---

## Key Indicators

**Attacker IP Addresses**
- `185.98.171.250` – Windows RDP access
- `185.98.171.249` – Linux SSH and cloud access
- `182.121.245.103` – Hosted malicious script

**Malware**
- Win32/Neshta.A

**Files Involved**
- `FileCoAuth.exe`
- `OneDriveLauncher.exe`
- `OneDriveStandaloneUpdater.exe`
- `/root/bin.sh`

**File Hash (SHA1)**
- `5857a7dd621c4c2ebb0b53ebc915d409f70d39f`

---

## Investigation Files

Use the links below to explore the investigation in more detail:

- **[Incident Report](Incident-Report.md)** – Client-facing summary and findings
- **[Investigation Report (Analyst Deep Dive)](Investigation-Report.md)**  
  Detailed analysis covering timeline creation, scoping, evidence review, MITRE ATT&CK mapping, and investigation decisions.  
- **[Investigation Walkthrough](Walkthrough.md)** – Step-by-step analyst decision process  
- **[Timeline](Timeline.csv)** – Event timeline of attacker activity  
- **[Indicators of Compromise](IOCs.csv)** – IPs, files, and hashes used for scoping  
- **[Scope Assessment](Scoping.md)** – Confirmation of containment  
- **[Hunting Queries](queries/)** – Queries used during investigation  

---

## Key Learning Points

- Credential-based attacks may not trigger brute-force alerts.
- Timelines help connect activity across endpoints, servers, and cloud services.
- **Scoping confirms how far the attacker actually went.**
- Inbox rules are a strong signal of email compromise.
- Clear documentation is a critical SOC skill.

---

## Notes

> **Note:** This investigation is a **simulated scenario** completed for practice, learning, and skill development.  
> The purpose is to improve incident response understanding, demonstrate investigative methodology, and share knowledge with the community.
