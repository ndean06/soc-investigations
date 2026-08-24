# MITRE ATT&CK Mapping: KCD-Web Nemesys Ransomware

## Summary

This investigation mapped observed ransomware activity on `KCD-Web` to MITRE ATT&CK techniques. The strongest mapped behaviors include suspected brute-force access, credential dumping through Mimikatz and LaZagne, command and PowerShell execution, Registry Run persistence, Defender impairment, IFEO registry modification, file discovery, recovery impairment, and ransomware impact activity.

---

## ATT&CK Mapping

| Tactic | Technique | Technique ID | Evidence | Confidence |
|---|---|---|---|---|
| Initial Access | Brute Force | T1110 | Repeated failed authentication attempts were observed prior to suspicious successful access involving the `receptionist` account and external IP `141[.]98[.]83[.]86`. | High |
| Initial Access | Valid Accounts | T1078 | The `KCD-Web\receptionist` account was used during the suspicious access and subsequent attacker activity. | High |
| Credential Access | OS Credential Dumping: LSASS Memory | T1003.001 | Mimikatz executed `sekurlsa::logonPasswords` after requesting `privilege::debug`. | High |
| Credential Access | OS Credential Dumping: Security Account Manager | T1003.002 | Mimikatz executed `lsadump::sam`, and LaZagne-related activity launched `reg.exe save` against the SAM, SYSTEM, and SECURITY registry hives. | High |
| Execution | Windows Command Shell | T1059.003 | `cmd.exe` executed `DC.exe /D`, `7ZSfx000.cmd`, and credential-dumping related commands. | High |
| Execution | PowerShell | T1059.001 | `nemesys.exe` launched PowerShell with `ExecutionPolicy Bypass` to dismount disk images and stop virtual machines. | High |
| Execution | Visual Basic | T1059.005 | `wscript.exe` executed `miparser.vbs` to process Mimikatz credential output. | High |
| Persistence | Registry Run Keys / Startup Folder | T1547.001 | `nemesys.exe` created a Run registry entry under the receptionist user hive. | High |
| Persistence | Event Triggered Execution: Image File Execution Options Injection | T1546.012 | `nemesys.exe` created numerous IFEO `Debugger` registry entries targeting Windows, database, backup, and administrative executables. | High |
| Defense Impairment | Impair Defenses | T1562.001 | `DC.exe` modified the Defender `DisableAntiSpyware` policy and later executed as `NT AUTHORITY\SYSTEM` using `/SYS 1`. | High |
| Discovery | File and Directory Discovery | T1083 | `nemesys.exe` launched `Everything.exe -startup`, consistent with rapid file indexing and discovery. | High |
| Impact | Inhibit System Recovery | T1490 | `nemesys.exe` modified VSS-related configuration and executed PowerShell commands affecting disk images and virtual machines. | High |
| Impact | Data Encrypted for Impact | T1486 | `nemesys.exe` executed and `Info_to_decrypt_nemesys.txt` was displayed; however, widespread file encryption was not conclusively identified in available telemetry. | Medium |

---

## Key Technique Notes

### T1110 - Brute Force

Repeated authentication failures were observed before the malicious activity. Suspicious authentication involving external IP `141[.]98[.]83[.]86` and the `receptionist` account was investigated as the likely access path.

The authentication pattern was consistent with brute-force or password-spraying activity preceding the compromise.

### T1078 - Valid Accounts

The `KCD-Web\receptionist` account was used during the attacker activity. Following suspicious authentication, tooling was staged and executed within the receptionist user context.

The available telemetry supports use of a valid account but does not conclusively identify how the correct password was obtained.

### T1003.001 - OS Credential Dumping: LSASS Memory

Mimikatz was executed with:

```text
privilege::debug
sekurlsa::logonPasswords
```

These commands support credential dumping from LSASS memory.

Mimikatz output was written to:

```text
.\!logs\Result.txt
```

The results were later parsed and reviewed.

### T1003.002 - OS Credential Dumping: Security Account Manager

Mimikatz executed:

```text
lsadump::sam
```

Additional credential-access activity associated with LaZagne launched commands to export credential-related registry hives:

```text
reg.exe save hklm\sam
reg.exe save hklm\system
reg.exe save hklm\security
```

This activity supports attempted extraction of locally stored credential material.

### T1059.003 - Windows Command Shell

`cmd.exe` was used throughout the ransomware execution chain.

Observed activity included:

```text
cmd.exe /c DC.exe /D
cmd.exe /c 7ZSfx000.cmd
```

Command shell activity also supported registry-hive export operations associated with credential dumping.

### T1059.001 - PowerShell

`nemesys.exe` launched PowerShell with execution-policy bypass to interfere with virtual machines and mounted disk images.

Observed commands included:

```text
powershell.exe -ExecutionPolicy Bypass "Get-Volume | Get-DiskImage | Dismount-DiskImage"

powershell.exe -ExecutionPolicy Bypass "Get-VM | Select-Object vmid | Get-VHD | %{Get-DiskImage -ImagePath $_.Path; Get-DiskImage -ImagePath $_.ParentPath} | Dismount-DiskImage"

powershell.exe -ExecutionPolicy Bypass "Get-VM | Stop-VM"
```

### T1059.005 - Visual Basic

`wscript.exe` executed:

```text
miparser.vbs
```

against:

```text
.\!logs\Result.txt
```

The script processed Mimikatz output before `Passwords.txt` and `NTLM.txt` were reviewed.

### T1547.001 - Registry Run Keys / Startup Folder

`nemesys.exe` created a Run registry entry under the receptionist user hive:

```text
HKU\...\Software\Microsoft\Windows\CurrentVersion\Run\nemesys
```

This provided a mechanism for `nemesys.exe` to execute again when the user logged on.

### T1546.012 - Image File Execution Options Injection

`nemesys.exe` modified numerous Image File Execution Options `Debugger` registry entries.

Examples included:

```text
Sysmon.exe
Sysmon64.exe
taskmgr.exe
taskkill.exe
perfmon.exe
shutdown.exe
sqlservr.exe
VeeamDeploymentSvc.exe
```

The volume and targeting of these modifications support deliberate interference with administrative, monitoring, backup, and application processes.

### T1562.001 - Impair Defenses

`DC.exe` modified:

```text
HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\DisableAntiSpyware
```

The value was set to:

```text
DWORD 1
```

`DC.exe` later executed as:

```text
NT AUTHORITY\SYSTEM
```

using:

```text
DC.exe /SYS 1
```

This strongly supports deliberate Microsoft Defender impairment.

### T1083 - File and Directory Discovery

`nemesys.exe` launched:

```text
Everything.exe -startup
```

from the ransomware staging directory.

Everything is a rapid file-indexing/search utility and was likely used to enumerate files available to the ransomware operation.

### T1490 - Inhibit System Recovery

Ransomware activity included VSS-related registry modification and PowerShell commands designed to dismount disk images and stop virtual machines.

These actions could interfere with recovery resources and make files or virtual systems unavailable during ransomware operations.

### T1486 - Data Encrypted for Impact

`nemesys.exe` executed and the ransom note:

```text
Info_to_decrypt_nemesys.txt
```

was displayed on `KCD-Web`.

These findings strongly support ransomware impact activity. However, the reviewed Sysmon telemetry did not show sufficient mass file-write, rename, or extension-change evidence to conclusively prove widespread file encryption.

For that reason, T1486 is mapped with **Medium confidence** rather than High.