# Investigation Report: KCD Ransomware - Shadow Copy Deletion

## Findings

### Case Summary

| Field | Details |
|---|---|
| **Time** | 2026-07-05 22:35:31 UTC |
| **Host** | `KCD-Web` |
| **Host IP** | `172.16.1.7` |
| **Affected User** | `KCD-Web\administrator` |
| **Alert Name** | KCD - Endpoint - Shadow Copy Deletion |
| **Suspicious Source IP** | `91[.]99[.]176[.]42` |
| **Additional Suspicious IP** | `91[.]238[.]181[.]47` |
| **Malware Classification** | Ransomware |
| **Defender Detection** | `Ransom:Win64/WannaCrypt.PAGV!MTB` |
| **Filename** | `Stub.exe` |
| **Ransom Note** | `DataRecovery.txt` |
| **Ransom Email** | `ransomhunters2026[at]gmail[.]com` |
| **Encrypted Filename Pattern** | `.[ransomhunters2026[at]gmail[.]com].fDKuxNIx` |
| **Final Determination** | **True Positive - Ransomware Activity Confirmed** |

### Malware Details

**File Path**

    C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe

**File Hashes**

| Hash Type | Value |
|---|---|
| **SHA256** | `1D7323941B44E77F22DD93701CAE781D330AA26881993B9D2307F1C783AC7CD7` |
| **SHA1** | `40BF54C808F2A2F2AEB000480A36CC117839FA4B` |
| **MD5** | `829BF5971E4808CB5D8ED52BBA328B59` |

### Primary Command Observed

    vssadmin Delete Shadows /All /Quiet

> **Final Determination:** **True Positive - Ransomware Activity Confirmed**

---

## Investigation

### Initial Access

On **2026-07-05 at 22:34:33 UTC**, the `KCD-Web\administrator` account successfully authenticated to `KCD-Web` from external IP `91[.]99[.]176[.]42` using **NTLM authentication**.

Shortly afterward, at **22:35:06 UTC**, a suspicious executable named `Stub.exe` was created at:

    C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe

### Ransomware Execution

At **22:35:31 UTC**, `Stub.exe` executed from the administrator's Documents directory.

The process launched `cmd.exe`, which executed:

    vssadmin Delete Shadows /All /Quiet

Additional recovery-impairment activity was observed, including:

- `wmic SHADOWCOPY /nointeractive`
- `bcdedit` commands targeting Windows recovery options

These actions were consistent with attempts to prevent recovery following ransomware execution.

### Ransomware Impact

At approximately **22:35:50 UTC**, `Stub.exe` created `DataRecovery.txt` ransom notes across multiple user and system-accessible directories on `KCD-Web`.

Encrypted filename artifacts were also observed using the following pattern:

    .[ransomhunters2026[at]gmail[.]com].fDKuxNIx

The combination of ransom-note creation, encrypted filename artifacts, and recovery impairment supported confirmed ransomware impact.

### Defender Detection and Quarantine

Microsoft Defender later detected the malware associated with `Stub.exe` as:

    Ransom:Win64/WannaCrypt.PAGV!MTB

Defender subsequently quarantined the detected threat.

### Collection and Exfiltration Review

Collection and exfiltration were reviewed through searches for:

- Archive and staging behavior
- Common exfiltration utilities
- Suspicious outbound network activity
- Common remote-access tools
- Secondary payload activity

No confirmed evidence of **data exfiltration**, **secondary remote-access tooling**, or an **exact inventory of encrypted files** was identified in the reviewed telemetry.

---

## 5W1H

| Question | Finding |
|---|---|
| **WHO** | The affected account was `KCD-Web\administrator`. Successful suspicious access originated from `91[.]99[.]176[.]42`. IP `91[.]238[.]181[.]47` generated failed RDP attempts but had no confirmed successful logon. |
| **WHAT** | Ransomware activity occurred on `KCD-Web`. `Stub.exe` executed, impaired recovery, attempted Run key persistence, created `DataRecovery.txt` ransom notes, and produced encrypted filename artifacts. |
| **WHEN** | Key activity occurred on **2026-07-05 between 22:34:33 UTC and 22:36:13 UTC**. `Stub.exe` executed at **22:35:31 UTC**. |
| **WHERE** | Activity occurred on `KCD-Web`. The payload was located at `C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe`. |
| **WHY** | The activity appeared intended to encrypt or otherwise impact files while preventing recovery. |
| **HOW** | The attacker likely used valid administrator access originating from `91[.]99[.]176[.]42`. `Stub.exe` then launched `vssadmin`, `wmic`, and `bcdedit` commands to impair recovery. Defender later detected and quarantined the malware as `Ransom:Win64/WannaCrypt.PAGV!MTB`. |

---

## Recommendations

### 1. Contain the Affected Host

Coordinate emergency containment of `KCD-Web`.

If operationally possible, isolate the endpoint immediately. If immediate isolation is not possible, restrict access to required services only until the system can be restored from a known-good backup.

### 2. Reset Exposed Credentials

Reset the `KCD-Web\administrator` account and rotate any credentials that may have been exposed on the compromised host, including:

- Local administrator credentials
- Service account credentials
- Application credentials
- Backup credentials
- Stored or cached credentials

### 3. Block Suspicious Infrastructure

Block the following IP addresses at the perimeter:

- `91[.]99[.]176[.]42`
- `91[.]238[.]181[.]47`

Treat `91[.]99[.]176[.]42` as the **confirmed suspicious access source**.

### 4. Harden Remote Access

Restrict RDP access to `KCD-Web` using controls such as:

- VPN access
- Multi-factor authentication
- Source IP allow-listing
- Limited administrative exposure

### 5. Validate Remediation and Recovery

Confirm:

- Microsoft Defender remediation completed successfully
- Backup integrity
- Availability of known-good restore points
- Extent of encrypted data
- Overall business impact

Ransomware impact was confirmed; however, **data exfiltration and the exact inventory of encrypted files were not confirmed**.

---

## Final Determination

> **True Positive - Ransomware Activity Confirmed**

The investigation confirmed ransomware execution on `KCD-Web` involving `Stub.exe`, recovery impairment, ransom-note creation, encrypted filename artifacts, and subsequent Microsoft Defender detection and quarantine.

Suspicious external access from `91[.]99[.]176[.]42` preceded execution and was associated with the affected administrator account. No confirmed evidence of data exfiltration or additional remote-access tooling was identified in the reviewed telemetry.