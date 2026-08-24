# KCD-Web Nemesys Ransomware Investigation

## Overview

This investigation analyzed ransomware activity on `KCD-Web` involving suspected credential compromise, credential dumping, malware staging, Defender impairment, persistence, and execution of `nemesys.exe`. The investigation focused on reconstructing the attack path, validating credential-access activity, identifying ransomware behavior, and determining the scope and impact of the incident.

## Case Summary

| Field | Details |
|---|---|
| Client | Kerning City Dental |
| Alert / Investigation | KCD-Web Nemesys Ransomware |
| Host | KCD-Web |
| User | KCD-Web\receptionist |
| Primary Suspicious IP | 141.98.83.86 |
| Payload | nemesys.exe |
| Credential Access Tools | Mimikatz, LaZagne |
| Final Determination | True Positive - Ransomware Activity Confirmed |

## Key Findings

- Suspicious authentication activity involving the `receptionist` account was observed before ransomware execution.
- The `automim1` toolkit was staged under the receptionist `Videos` directory and contained Mimikatz, LaZagne, and additional credential-recovery tools.
- Mimikatz executed `sekurlsa::logonPasswords` and `lsadump::sam`, confirming credential-dumping activity.
- Credential results were written to `Result.txt`, parsed with `miparser.vbs`, and reviewed through `Passwords.txt` and `NTLM.txt`.
- `nemesys.exe` executed and staged supporting components in a randomly named `AppData\Local` directory.
- `DC.exe` impaired Microsoft Defender and later executed under `NT AUTHORITY\SYSTEM`.
- `Everything.exe -startup` was launched for file discovery.
- Registry Run persistence and multiple IFEO Debugger registry modifications were observed.
- `Info_to_decrypt_nemesys.txt` was displayed as the ransom note.
- Widespread file encryption was not conclusively confirmed in the available telemetry.
- Scoping did not identify definitive evidence of ransomware propagation to additional hosts.

## MITRE ATT&CK

| Tactic | Technique |
|---|---|
| Initial Access | Brute Force |
| Credential Access | OS Credential Dumping |
| Execution | Command and Scripting Interpreter |
| Persistence | Registry Run Keys / Startup Folder |
| Privilege Escalation | Elevated Execution |
| Defense Impairment | Impair Defenses |
| Discovery | File and Directory Discovery |
| Impact | Data Encrypted for Impact - Suspected |

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
- Sysmon process, registry, and file analysis
- Credential-dumping investigation
- Attack-chain reconstruction
- Timeline creation
- IOC and behavior-based scoping
- MITRE ATT&CK mapping
- Incident reporting