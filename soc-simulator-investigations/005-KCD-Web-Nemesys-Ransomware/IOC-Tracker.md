# IOC Tracker: KCD-Web Nemesys Ransomware

## Network Indicators

| Indicator | Type | Context | Confidence |
|---|---|---|---|
| 141[.]98[.]83[.]86 | External IP | Suspicious authentication activity associated with the suspected access path to KCD-Web | High |

## Accounts

| Indicator | Type | Context | Confidence |
|---|---|---|---|
| KCD-Web\receptionist | User Account | Account associated with attacker tooling, credential dumping, and ransomware execution | High |
| NT AUTHORITY\SYSTEM | Execution Context | Used by DC.exe during elevated defense impairment activity | High |

## Malicious / Suspicious Files

| Indicator | Type | Context | Confidence |
|---|---|---|---|
| nemesys.exe | Executable | Primary ransomware-associated payload | High |
| DC.exe | Executable | Used for Microsoft Defender impairment and SYSTEM-level activity | High |
| mimikatz.exe | Executable | Used for credential dumping | High |
| miparser.vbs | Script | Parsed Mimikatz credential output | High |
| 7ZSfx000.cmd | Batch Script | Ransomware-associated orchestration script | High |
| xdel.exe | Executable | Component extracted with the ransomware toolset | Medium |
| Info_to_decrypt_nemesys.txt | Ransom Note | Nemesys ransom note displayed on KCD-Web | High |
| Result.txt | Output File | Mimikatz credential-dumping output | High |
| Passwords.txt | Output File | Parsed credential results opened after Mimikatz execution | High |
| NTLM.txt | Output File | Parsed NTLM credential/hash results opened after Mimikatz execution | High |

## Supporting Tools

| Indicator | Type | Context |
|---|---|---|
| LaZagne | Credential Access Tool | Present in automim1 toolkit and associated with credential-access activity |
| Everything.exe | Discovery Tool | Executed with `-startup` during ransomware operations |
| 7za.exe | Archive Utility | Used to extract ransomware components |

> These tools may have legitimate uses but were suspicious because of their execution context and relationship to the confirmed malicious activity.

## File Paths

| Indicator | Context | Confidence |
|---|---|---|
| C:\Users\receptionist\Videos\automim1\ | Attacker tooling/staging directory | High |
| C:\Users\receptionist\Videos\automim1\automim1\mimikatz\x64\mimikatz.exe | Executed Mimikatz binary | High |
| C:\Users\receptionist\AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\ | Nemesys ransomware staging/working directory | High |
| C:\Users\receptionist\AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\nemesys.exe | Staged Nemesys executable | High |
| C:\Users\receptionist\AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\DC.exe | Staged defense impairment tool | High |
| C:\Users\receptionist\AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\Everything.exe | Staged discovery utility | High |
| C:\Users\receptionist\AppData\Local\Temp\2\7ZipSfx.000\ | Temporary ransomware extraction directory | High |

## Registry Indicators

| Indicator | Context | Confidence |
|---|---|---|
| Registry Run key associated with nemesys.exe | Persistence | High |
| HKCR\mimicfile\shell\open\command\(Default) | File association modification associated with ransom-note behavior | High |
| DisableAntiSpyware | Microsoft Defender impairment | High |
| Image File Execution Options (IFEO) Debugger modifications | Process interference / defense impairment | High |

## Behavioral Indicators

| Behavior | Evidence |
|---|---|
| Credential Dumping | `sekurlsa::logonPasswords` and `lsadump::sam` |
| Privilege Manipulation | `privilege::debug` and `token::elevate` |
| Credential Output Parsing | `miparser.vbs` processed `Result.txt` |
| Defender Impairment | `DC.exe` and `DisableAntiSpyware` activity |
| SYSTEM-Level Execution | `DC.exe /SYS 1` executed as `NT AUTHORITY\SYSTEM` |
| File Discovery | `Everything.exe -startup` |
| Persistence | Registry Run key creation |
| Process Interference | IFEO Debugger registry modifications |
| Ransomware Impact | `Info_to_decrypt_nemesys.txt` ransom note displayed |

## Scoping Notes

- Nemesys-related malicious activity was primarily identified on `KCD-Web`.
- Behavior-based scoping included credential dumping, Defender impairment, ransom-note artifacts, registry modifications, and ransomware-associated tooling.
- No definitive evidence of ransomware propagation to additional hosts was identified in the reviewed telemetry.
- Widespread file encryption was not conclusively confirmed from the available endpoint telemetry.
- File hashes were not identified in the reviewed evidence and therefore are not included as confirmed IOCs.