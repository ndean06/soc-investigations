# KCD Ransomware Investigation: Shadow Copy Deletion

## Overview

This investigation analyzed a ransomware-related alert on `KCD-Web` involving shadow copy deletion through `vssadmin.exe`. The investigation focused on identifying the access path, validating ransomware execution, reviewing persistence, assessing impact, and determining whether collection or exfiltration activity was observed.

## Case Summary

| Field | Details |
|---|---|
| Client | Kerning City Dental |
| Alert | KCD - Endpoint - Shadow Copy Deletion |
| Host | KCD-Web |
| User | KCD-Web\administrator |
| Primary Suspicious IP | 91.99.176.42 |
| Payload | Stub.exe |
| Final Determination | True Positive - Ransomware Activity Confirmed |

## Key Findings

- Successful administrator/RDP access was observed from `91.99.176.42` shortly before ransomware execution.
- `Stub.exe` was created and executed from the administrator Documents directory.
- `Stub.exe` launched recovery-impairment commands including `vssadmin`, `wmic`, and `bcdedit`.
- `DataRecovery.txt` ransom notes were created across multiple directories.
- Encrypted filename artifacts containing `[ransomhunters2026@gmail.com].fDKuxNIx` were observed.
- Defender detected and quarantined the malware as `Ransom:Win64/WannaCrypt.PAGV!MTB`.
- Collection and exfiltration were reviewed, but not confirmed in the available telemetry.

## Investigation Artifacts

| Artifact | Description |
|---|---|
| [Investigation Report](./Investigation-Report.md) | Final SOC-style investigation report |
| [Walkthrough](./Walkthrough.md) | Analyst investigation process and pivots |
| [Timeline](./Timeline.md) | Key event timeline |
| [IOC Tracker](./IOC-Tracker.md) | Indicators of compromise |
| [MITRE Mapping](./MITRE-Mapping.md) | ATT&CK tactic and technique mapping |
| [Query Log](./Query-Log.md) | Splunk queries used during the investigation |

## Skills Demonstrated

- Ransomware investigation
- Splunk log analysis
- Windows Security event review
- Sysmon process and file telemetry analysis
- Timeline creation
- IOC scoping
- MITRE ATT&CK mapping
- Exfiltration validation
- Client-facing reporting