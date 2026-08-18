# Walkthrough: KCD Ransomware - Shadow Copy Deletion

## Investigation Goal

The goal of this investigation was to determine whether the shadow copy deletion alert on `KCD-Web` represented true ransomware activity, identify the access path, validate execution and impact, and review whether collection or exfiltration activity was present in the available telemetry.

---

## Screenshot Evidence Guide

Screenshots are included throughout this walkthrough to support the investigation steps and show the analyst workflow from alert review through final determination.

Recommended screenshot folder:

```text
./screenshots/
```

Before adding screenshots to GitHub, screenshots should be cropped to remove browser session URLs, Splunk session links, tokens, credentials, unrelated customer data, or unnecessary raw logs.

---

## 1. Alert Review

The investigation began with a Splunk scheduled detection for shadow copy deletion on `KCD-Web`.

The alert centered on the following command:

`vssadmin Delete Shadows /All /Quiet`

This command is commonly associated with ransomware because it removes Windows shadow copies, reducing the victim's ability to restore files.

**Initial question:**  
Was this a legitimate administrative action or ransomware-related recovery impairment?

### Screenshot Evidence

![Shadow copy deletion alert showing vssadmin execution](./screenshots/01-shadow-copy-alert.png)

---

## 2. Initial ATT&CK Mapping

The first technique considered was:

| Tactic | Technique | Technique ID |
|---|---|---|
| Defense Impairment | Inhibit System Recovery | T1490 |

The alert matched recovery impairment behavior because shadow copies were deleted using `vssadmin`.

---

## 3. Process Chain Review

The next pivot was to identify what launched `vssadmin.exe`.

The process chain showed:

`Stub.exe -> cmd.exe -> vssadmin.exe`

This shifted the investigation from a single suspicious command to a likely ransomware execution chain.

**Key finding:**  
`Stub.exe` was the parent process responsible for launching recovery-impairment commands.

### Screenshot Evidence

![Process chain showing Stub.exe launching cmd.exe and vssadmin.exe](./screenshots/02-process-chain-stub-cmd-vssadmin.png)

---

## 4. Payload Review

`Stub.exe` was observed executing from the administrator Documents directory:

`C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe`

Associated hashes were identified:

| Hash Type | Value |
|---|---|
| SHA256 | `1D7323941B44E77F22DD93701CAE781D330AA26881993B9D2307F1C783AC7CD7` |
| SHA1 | `40BF54C808F2A2F2AEB000480A36CC117839FA4B` |
| MD5 | `829BF5971E4808CB5D8ED52BBA328B59` |

Microsoft Defender later detected the threat as:

`Ransom:Win64/WannaCrypt.PAGV!MTB`

**Conclusion:**  
`Stub.exe` was confirmed as the primary ransomware payload in the reviewed telemetry.

### Screenshot Evidence

![Stub.exe payload path and execution evidence](./screenshots/03-stub-exe-payload-path.png)

---

## 5. Access Path Review

After identifying the payload, the investigation pivoted backward to determine how access may have occurred.

Successful authentication activity was observed from:

`91[.]99[.]176[.]42`

This IP was associated with successful administrator authentication and RDP reconnect activity shortly before `Stub.exe` executed.

A second IP, `91[.]238[.]181[.]47`, showed high-volume RDP failed-authentication activity, but no confirmed successful logon was identified from that source.

**Conclusion:**  
`91[.]99[.]176[.]42` was treated as the confirmed suspicious access IP.  
`91[.]238[.]181[.]47` was treated as a suspicious brute-force/password-guessing source.

### Screenshot Evidence

![Suspicious administrator RDP access from external IP](./screenshots/04-suspicious-rdp-access.png)

---

## 6. Recovery Impairment Review

Multiple recovery-impairment commands were observed after `Stub.exe` execution:

- `vssadmin Delete Shadows /All /Quiet`
- `wmic SHADOWCOPY /nointeractive`
- `bcdedit /set {default} recoveryenabled No`
- `bcdedit /set {default} bootstatuspolicy ignoreallfailures`

These commands indicate an attempt to prevent or weaken system recovery.

**ATT&CK Mapping:**  
Defense Impairment — `T1490: Inhibit System Recovery`

### Screenshot Evidence

![Recovery impairment commands showing vssadmin wmic and bcdedit activity](./screenshots/05-recovery-impairment-commands.png)

---

## 7. Persistence Review

The investigation then reviewed whether the payload attempted to persist.

`Stub.exe` modified the following Run key:

`HKCU\Software\Microsoft\Windows\CurrentVersion\Run\F56A2BB52AF4B409`

The registry value data pointed back to:

`C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe`

Microsoft Defender later removed the persistence artifact.

**ATT&CK Mapping:**  
Persistence — `T1547.001: Registry Run Keys / Startup Folder`

### Screenshot Evidence

![Run key persistence artifact pointing back to Stub.exe](./screenshots/06-run-key-persistence.png)

---

## 8. Impact Review

Ransom note activity was observed shortly after execution.

The ransom note name was:

`DataRecovery.txt`

Ransomware-style encrypted filename artifacts were also observed with the pattern:

`.[ransomhunters2026[at]gmail[.]com].fDKuxNIx`

These artifacts support ransomware impact, although the exact original encrypted file paths were not identified in the reviewed telemetry.

**ATT&CK Mapping:**  
Impact — `T1486: Data Encrypted for Impact`

### Screenshot Evidence

![Ransom note and ransomware filename pattern evidence](./screenshots/07-ransom-note-and-file-pattern.png)

---

## 9. Defender Detection and Quarantine

Microsoft Defender detected and quarantined the malware associated with `Stub.exe`.

Detection name:

`Ransom:Win64/WannaCrypt.PAGV!MTB`

This supported the final determination that the alert was not just suspicious administrative activity, but confirmed ransomware activity.

### Screenshot Evidence

![Defender detection and quarantine for ransomware](./screenshots/08-defender-detection-quarantine.png)

---

## 10. Collection and Exfiltration Review

The investigation reviewed for evidence of collection, staging, upload tooling, suspicious outbound traffic, and secondary remote access tooling.

The review included searches for:

- Archive utilities
- File staging behavior
- Common exfiltration tools
- Suspicious outbound network traffic
- RMM and secondary remote access tools

No confirmed attacker-controlled archive creation, data staging, exfiltration tooling, suspicious outbound traffic, or secondary remote access tooling was identified in the reviewed telemetry.

**Conclusion:**  
Collection and exfiltration were reviewed but not confirmed.

### Screenshot Evidence

![Exfiltration review showing no confirmed collection or outbound exfiltration evidence](./screenshots/09-exfil-review-no-confirmed-evidence.png)

---

## 11. Final Timeline Review

After validating the major pivots, the final timeline showed the ransomware sequence in order:

1. Suspicious administrator/RDP access from `91[.]99[.]176[.]42`
2. `Stub.exe` creation and execution
3. Shadow copy deletion and recovery impairment
4. Run key persistence attempt
5. Ransom note creation
6. Defender detection and quarantine
7. Encrypted filename artifacts observed

### Screenshot Evidence

![Final event timeline showing ransomware activity sequence](./screenshots/10-final-event-timeline.png)

---

## 12. Final Determination

This alert was determined to be a true positive ransomware event.

The strongest evidence included:

- Suspicious administrator/RDP access from `91[.]99[.]176[.]42`
- Execution of `Stub.exe`
- Shadow copy deletion with `vssadmin`
- Additional recovery impairment using `wmic` and `bcdedit`
- Run key persistence attempt
- Ransom note deployment
- Encrypted filename artifacts
- Defender detection and quarantine as `Ransom:Win64/WannaCrypt.PAGV!MTB`

## Final Classification

**True Positive - Ransomware Activity Confirmed**

---

## Lessons Learned

- Shadow copy deletion should be treated as high-priority when tied to suspicious parent processes.
- Process ancestry is critical for separating administrative activity from ransomware behavior.
- Successful logon activity immediately before payload execution can help identify the likely access path.
- Recovery impairment, persistence, ransom notes, and encrypted filename artifacts together provide strong evidence of ransomware impact.
- Collection and exfiltration should be reviewed separately and not assumed without supporting evidence.