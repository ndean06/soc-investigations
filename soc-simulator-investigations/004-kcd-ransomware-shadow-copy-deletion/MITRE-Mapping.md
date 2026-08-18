# MITRE ATT&CK Mapping: KCD Ransomware - Shadow Copy Deletion

## Summary

This investigation mapped observed ransomware activity on `KCD-Web` to MITRE ATT&CK techniques. The strongest mapped behaviors include suspicious RDP access, malicious payload execution, recovery impairment, Run key persistence, ransom note deployment, and encrypted filename artifacts.

---

## ATT&CK Mapping

| Tactic | Technique | Technique ID | Evidence | Confidence |
|---|---|---|---|---|
| Initial Access | External Remote Services | T1133 | Successful administrator authentication and RDP reconnect activity from `91[.]99[.]176[.]42` shortly before ransomware execution. | High |
| Initial Access | Valid Accounts | T1078 | `KCD-Web\administrator` was used for successful authentication before `Stub.exe` execution. | High |
| Execution | User Execution: Malicious File | T1204.002 | `Stub.exe` was created and executed from the administrator Documents directory. | Medium |
| Execution | Windows Command Shell | T1059.003 | `Stub.exe` launched `cmd.exe` to execute recovery-impairment commands. | High |
| Defense Impairment | Inhibit System Recovery | T1490 | `vssadmin Delete Shadows /All /Quiet`, `wmic SHADOWCOPY /nointeractive`, and `bcdedit` recovery changes were observed. | High |
| Persistence | Registry Run Keys / Startup Folder | T1547.001 | `Stub.exe` modified an HKCU Run key named `F56A2BB52AF4B409` pointing back to the payload. | High |
| Impact | Data Encrypted for Impact | T1486 | Ransom notes and encrypted filename artifacts containing `[ransomhunters2026@gmail.com].fDKuxNIx` were observed. | High |
| Impact | Data Destruction | T1485 | Commands were observed deleting `$RECYCLE.BIN` and `Recycler` directories across multiple drive letters. | Medium |

---

## Key Technique Notes

### T1133 - External Remote Services

Suspicious access was observed from external IP `91[.]99[.]176[.]42`. This activity included successful administrator authentication and RDP reconnect activity shortly before ransomware execution.

### T1078 - Valid Accounts

The `KCD-Web\administrator` account was used during the suspicious access window. The available telemetry supports valid credential use, but does not confirm how the credentials were obtained.

### T1204.002 - User Execution: Malicious File

`Stub.exe` was created and executed from the administrator Documents directory. This supports malicious file execution, though the exact user interaction method was not confirmed.

### T1059.003 - Windows Command Shell

`Stub.exe` launched `cmd.exe`, which then executed commands used to impair recovery and support ransomware activity.

### T1490 - Inhibit System Recovery

The ransomware chain attempted to prevent system recovery by deleting shadow copies and modifying recovery settings.

Observed commands included:

```text
vssadmin Delete Shadows /All /Quiet
wmic SHADOWCOPY /nointeractive
bcdedit /set {default} recoveryenabled No
bcdedit /set {default} bootstatuspolicy ignoreallfailures

