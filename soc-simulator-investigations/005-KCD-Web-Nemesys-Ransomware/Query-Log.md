# Query Log: KCD-Web Nemesys Ransomware

## Purpose

This file documents the main Splunk searches used during the investigation. Queries were sanitized for GitHub publication where appropriate. Replace placeholder values as needed when reusing the searches.

---

## Query 1: Suspicious Authentication Review

**Question:** Was suspicious authentication activity observed before the ransomware activity?

```spl
index=endpoint earliest="04/01/2026:00:00:00" latest="04/01/2026:23:59:59" host="KCD-Web"
(EventCode=4624 OR EventCode=4625) Source_Network_Address="141.98.83.86"
| table _time EventCode Account_Name Logon_Type Source_Network_Address Workstation_Name Failure_Reason Status Sub_Status
| sort +_time
```

**Finding:** Repeated failed authentication activity from `141[.]98[.]83[.]86` was identified during review of the suspected access path involving the `receptionist` account.

---

## Query 2: automim1 Tooling Review

**Question:** When did the `automim1` toolkit appear on the affected host?

```spl
index=endpoint earliest="04/01/2026:18:07:00" latest="04/01/2026:18:17:00" host="KCD-Web" "automim1"
| table _time EventID User process_name Image CommandLine parent_process_name TargetFilename
| sort +_time
```

**Finding:** At `18:12:43 UTC`, Explorer created files within `C:\Users\receptionist\Videos\automim1\`, including LaZagne-related tooling and `!start.cmd`.

---

## Query 3: LaZagne Credential Access Review

**Question:** Did the staged LaZagne tooling perform credential-access activity?

```spl
index=endpoint earliest="04/01/2026:18:12:40" latest="04/01/2026:18:14:00" host="KCD-Web"
("lazagne" OR "reg.exe save" OR "hklm\\sam" OR "hklm\\system" OR "hklm\\security")
| table _time EventID User process_name Image CommandLine parent_process_name TargetFilename
| sort +_time
```

**Finding:** `lazagne.exe` spawned `cmd.exe`, which executed `reg.exe save` against the SAM, SYSTEM, and SECURITY registry hives.

---

## Query 4: Mimikatz Execution Review

**Question:** Was Mimikatz only staged, or was it actually executed?

```spl
index=endpoint earliest="04/01/2026:18:07:00" latest="04/01/2026:18:17:00" host="KCD-Web" "mimikatz"
| table _time EventID User process_name Image CommandLine TargetFilename
| sort +_time
```

**Finding:** At `18:12:53 UTC`, `mimikatz.exe` executed with `privilege::debug`, `sekurlsa::logonPasswords`, `token::elevate`, and `lsadump::sam`, confirming credential-dumping activity.

---

## Query 5: Credential Dump Output Review

**Question:** What happened to the credential output produced by Mimikatz?

```spl
index=endpoint earliest="04/01/2026:18:12:50" latest="04/01/2026:18:14:00" host="KCD-Web"
("Result.txt" OR "Passwords.txt" OR "NTLM.txt" OR "miparser.vbs")
| table _time source EventID process_name Image CommandLine parent_process_name TargetFilename User
| sort +_time
```

**Finding:** Mimikatz wrote output to `Result.txt`. `miparser.vbs` processed the output, after which `Passwords.txt`, `NTLM.txt`, and `Result.txt` were opened with Notepad.

---

## Query 6: Nemesys File Activity Review

**Question:** What files and supporting components were created when `nemesys.exe` executed?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web" EventID=11
| table _time process_name Image TargetFilename User
| sort +_time
```

**Finding:** `nemesys.exe` and `7za.exe` staged and extracted ransomware-associated components including `DC.exe`, `Everything.exe`, `xdel.exe`, and additional copies of `nemesys.exe`.

---

## Query 7: Nemesys Staging Directory Review

**Question:** Where did the ransomware establish its working directory?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web"
"1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA"
| table _time EventID process_name Image CommandLine TargetFilename User
| sort +_time
```

**Finding:** The ransomware staged `nemesys.exe`, `DC.exe`, `Everything.exe`, `7za.exe`, and related components within the randomly named `AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\` directory.

---

## Query 8: Registry Persistence Review

**Question:** Did `nemesys.exe` establish persistence?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web"
EventID=13 ("CurrentVersion\\Run" OR "nemesys")
| table _time EventID process_name Image TargetObject Details User
| sort +_time
```

**Finding:** `nemesys.exe` created Registry Run persistence associated with the `receptionist` user.

---

## Query 9: IFEO Registry Modification Review

**Question:** Did the ransomware interfere with Windows, security, backup, or administrative processes?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web"
EventID=13 "Image File Execution Options"
| table _time process_name Image TargetObject Details User
| sort +_time
```

**Finding:** `nemesys.exe` created multiple IFEO `Debugger` registry modifications targeting Windows, administrative, monitoring, database, and backup-related executables.

---

## Query 10: Defender Impairment Review

**Question:** Was Microsoft Defender intentionally impaired?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web"
("DC.exe" OR "DisableAntiSpyware")
| table _time EventID User process_name Image CommandLine parent_process_name TargetObject Details TargetFilename
| sort +_time
```

**Finding:** `DC.exe` was associated with Microsoft Defender impairment, including modification of `DisableAntiSpyware`. `DC.exe` later executed under `NT AUTHORITY\SYSTEM`.

---

## Query 11: SYSTEM-Level Execution Review

**Question:** Did ransomware-associated tooling execute with SYSTEM privileges?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web"
(process_name="DC.exe" OR Image="*\\DC.exe")
| table _time EventID User process_name Image CommandLine parent_process_name TargetFilename
| sort +_time
```

**Finding:** `DC.exe` executed as `NT AUTHORITY\SYSTEM`, supporting SYSTEM-level defense impairment activity.

---

## Query 12: Everything File Discovery Review

**Question:** Was file discovery tooling executed during the ransomware activity?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web"
("Everything.exe" OR "Everything64.dll" OR "Everything32.dll")
| table _time EventID User process_name Image CommandLine parent_process_name TargetFilename
| sort +_time
```

**Finding:** `Everything.exe` was staged and executed with the `-startup` parameter, consistent with rapid file discovery and indexing.

---

## Query 13: Ransom Note Review

**Question:** Was a Nemesys ransom note deployed or displayed?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:30:00" host="KCD-Web"
"Info_to_decrypt_nemesys.txt"
| table _time source EventID process_name Image CommandLine TargetObject Details TargetFilename User
| sort +_time
```

**Finding:** `Info_to_decrypt_nemesys.txt` was opened through `notepad.exe`, and related `nemesys.exe` registry activity was observed shortly afterward.

---

## Query 14: Encryption / Mass File Activity Review

**Question:** Could widespread file encryption be confirmed from available endpoint telemetry?

```spl
index=endpoint earliest="04/01/2026:18:14:40" latest="04/01/2026:18:45:00" host="KCD-Web"
(process_name="nemesys.exe" OR process_name="ENC_default*" OR process_name="Everything.exe")
(EventID=11 OR EventID=23 OR EventID=26)
| table _time EventID process_name Image TargetFilename User
| sort +_time
```

**Finding:** Ransomware-associated file activity was observed, but the reviewed telemetry did not provide sufficient mass file-write, rename, or extension-change evidence to conclusively confirm widespread encryption.

---

## Query 15: Post-Credential-Dump Authentication Review

**Question:** Were obvious new account logons observed immediately after credential dumping?

```spl
index=endpoint earliest="04/01/2026:18:12:53" latest="04/01/2026:18:30:00" host="KCD-Web"
(EventCode=4624 OR EventCode=4648 OR EventCode=4672)
| table _time EventCode Account_Name Logon_Type Source_Network_Address Workstation_Name Process_Name
| sort +_time
```

**Finding:** The reviewed events primarily showed SYSTEM service logons and special-privilege events. No clear evidence of dumped credentials being used for a new interactive or network logon was identified in this window.

---

## Query 16: Enterprise Ransomware Scoping

**Question:** Did the Nemesys activity appear on additional hosts?

```spl
index=endpoint earliest="03/25/2026:00:00:00" latest="04/08/2026:23:59:59"
("mimikatz.exe" OR "DC.exe" OR "Everything.exe" OR "Info_to_decrypt_nemesys.txt"
OR "DisableAntiSpyware" OR "Image File Execution Options"
OR "7ZipSfx.000" OR "sekurlsa::logonPasswords")
| stats count values(process_name) as processes values(Image) as images values(User) as users by host
| sort - count
```

**Finding:** The strongest combination of ransomware, credential-dumping, and defense-impairment behaviors was concentrated on `KCD-Web`. No definitive evidence of Nemesys ransomware propagation to additional hosts was identified in the reviewed telemetry.

---

## Query Log Summary

| Area | Result |
|---|---|
| Initial Access | Suspicious authentication activity involving `141[.]98[.]83[.]86` and the `receptionist` account was identified before ransomware activity. |
| Tool Staging | The `automim1` toolkit was staged under the receptionist `Videos` directory. |
| Credential Access | Mimikatz and LaZagne-related activity confirmed credential-dumping behavior. |
| Credential Output | Mimikatz output was parsed and credential result files were subsequently reviewed. |
| Ransomware Execution | `nemesys.exe` executed and established an AppData working/staging directory. |
| Persistence | Registry Run persistence was identified. |
| Defense Impairment | `DC.exe`, `DisableAntiSpyware`, and IFEO modifications supported deliberate security-control impairment. |
| Privileged Execution | `DC.exe` executed under `NT AUTHORITY\SYSTEM`. |
| Discovery | `Everything.exe -startup` supported rapid file discovery/indexing. |
| Impact | Ransom-note deployment was confirmed; widespread file encryption was not conclusively confirmed. |
| Credential Reuse | No clear post-dump interactive/network use of dumped credentials was identified in the reviewed window. |
| Scoping | No definitive evidence of Nemesys ransomware propagation to additional hosts was identified. |