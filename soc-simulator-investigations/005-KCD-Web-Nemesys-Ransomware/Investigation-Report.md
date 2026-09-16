# Investigation Report: KCD-Web — Nemesys Ransomware

> **Final determination:** True Positive — Ransomware Activity Confirmed  
> **Evidence limitation:** Widespread file encryption was not conclusively demonstrated in the available telemetry.

## 1. Incident Summary

| Field | Finding |
|---|---|
| **Host** | KCD-Web |
| **Affected account** | `KCD-Web\receptionist` |
| **Activity date** | April 1, 2026 |
| **Primary activity window** | Approximately 18:12:43–18:14:59 UTC |
| **Payload execution** | 18:14:42 UTC |
| **Suspicious source IP** | `141[.]98[.]83[.]86` |
| **Possible malware family** | Nemesys |
| **Primary payload** | `nemesys.exe` |
| **Observed scope** | Primarily KCD-Web; propagation to other hosts was not confirmed |

The investigation identified credential dumping, ransomware execution, Registry Run persistence, security-control impairment, file discovery, and ransom-note deployment on **KCD-Web**.

Suspicious authentication activity involving the receptionist account was reviewed as a possible initial access path. The available evidence confirmed ransomware-related activity but did not conclusively establish widespread encryption.

## 2. Key Artifacts

### Tools and Files

| Category | Observed artifacts |
|---|---|
| **Credential access** | Mimikatz, LaZagne |
| **Defense impairment** | `DC.exe` |
| **File discovery** | `Everything.exe` |
| **Component extraction** | `7za.exe` |
| **Ransomware-associated script** | `7ZSfx000.cmd` |
| **Ransom note** | `Info_to_decrypt_nemesys.txt` |
| **Credential output** | `Result.txt`, `Passwords.txt`, `NTLM.txt` |
| **Credential-output processing** | `miparser.vbs` |

### Staging Directories

**Credential-access toolkit**

```text
C:\Users\receptionist\Videos\automim1\
```

**Ransomware components**

```text
C:\Users\receptionist\AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\
```

### Credential-Dumping Commands

Observed Mimikatz commands:

- `privilege::debug`
- `sekurlsa::logonPasswords`
- `token::elevate`
- `lsadump::sam`

## 3. Investigation Findings

### Suspected Initial Access

Suspicious authentication activity involving `KCD-Web\receptionist` preceded the ransomware activity. Repeated failed authentication attempts associated with `141[.]98[.]83[.]86` were reviewed as part of the suspected access path.

**Assessment:** The authentication activity warranted investigation, but the initial access path remained suspected.

### Tool Staging and Credential Access

At **18:12:43 UTC**, attacker tooling was staged under the `automim1` directory. The toolkit contained credential-access utilities, including Mimikatz and LaZagne.

At **18:12:53 UTC**, `mimikatz.exe` executed with commands for debug privileges, logon-password extraction, token elevation, and SAM dumping.

Output was written to `Result.txt` and later processed by `miparser.vbs`. The resulting `Passwords.txt` and `NTLM.txt` files were subsequently opened and reviewed.

**Assessment:** Credential-dumping activity was confirmed.

### Ransomware Execution and Persistence

At **18:14:42 UTC**, `nemesys.exe` executed on KCD-Web. Supporting components were extracted using `7za.exe` and staged in the randomly named `AppData\Local` directory.

Observed follow-on activity included:

- Staging of `DC.exe` and `Everything.exe`.
- Establishment of Registry Run persistence.
- Multiple Image File Execution Options (**IFEO**) Debugger registry modifications.

### File Discovery and Defense Impairment

At approximately **18:14:46 UTC**, `Everything.exe` launched with the `-startup` parameter, consistent with file discovery and indexing.

`DC.exe` performed Microsoft Defender impairment activity, including modification of the `DisableAntiSpyware` policy. It later executed as `NT AUTHORITY\SYSTEM` with the `/SYS 1` parameter.

**Assessment:** Security-control impairment included execution under the SYSTEM account.

### Ransom-Note Deployment

At **18:14:56 UTC**, the ransomware-associated batch script `7ZSfx000.cmd` executed. The ransom note `Info_to_decrypt_nemesys.txt` was displayed through `notepad.exe`.

**Assessment:** Ransom-note deployment was confirmed on KCD-Web.

### Encryption Validation and Environment Scoping

File creation, deletion, and related endpoint telemetry were reviewed for evidence of mass encryption.

Broader scoping included searches for:

- Ransomware tooling and ransom-note indicators.
- Credential-dumping behavior.
- Microsoft Defender impairment artifacts.
- Registry modifications and related behavioral indicators.

**Findings and limitations:**

- Ransomware execution and preparation were confirmed.
- Available telemetry did not conclusively demonstrate widespread file encryption.
- Malicious activity was primarily identified on KCD-Web.
- No definitive evidence of ransomware propagation to additional hosts was identified within the reviewed telemetry.

## 4. Event Timeline

All times are in **UTC** on **April 1, 2026**.

| Time | Observed activity |
|---|---|
| Before payload execution | Suspicious authentication activity involving the receptionist account was reviewed |
| 18:12:43 | Credential-access tooling staged in the `automim1` directory |
| 18:12:53 | Mimikatz executed credential-dumping commands |
| 18:14:42 | `nemesys.exe` executed |
| Approximately 18:14:46 | `Everything.exe` launched with `-startup` |
| 18:14:56 | `7ZSfx000.cmd` executed; ransom note displayed through Notepad |

## 5. 5W1H Summary

| Question | Assessment |
|---|---|
| **Who?** | The affected account was `KCD-Web\receptionist`. The attacker’s identity was not established. |
| **What?** | Credential dumping, ransomware execution, persistence, IFEO modifications, Defender impairment, file discovery, and ransom-note deployment. |
| **When?** | April 1, 2026, primarily between approximately 18:12:43 and 18:14:59 UTC. |
| **Where?** | KCD-Web, with tooling staged in the receptionist profile’s `Videos\automim1` and `AppData\Local` directories. |
| **Why?** | The activity appeared intended to obtain credentials, impair security controls, establish persistence, discover files, and execute ransomware. |
| **How?** | Suspected account access was followed by tool staging, credential access, payload execution, component extraction, security-control impairment, and ransom-note deployment. |

## 6. Recommendations

1. **Contain the host and preserve evidence.**  
   Isolate KCD-Web and preserve relevant endpoint, authentication, registry, and filesystem evidence before remediation or reimaging.

2. **Reset potentially exposed credentials.**  
   Reset the receptionist account’s credentials and rotate administrator, service, application, or stored credentials that may have been exposed through Mimikatz or LaZagne.

3. **Block and investigate suspicious access.**  
   Block `141[.]98[.]83[.]86` and review authentication telemetry for additional suspicious sources, including failed attempts followed by successful authentication.

4. **Hunt for related activity across the environment.**  
   Search for the identified payload, credential-access tools, ransom note, Defender policy changes, IFEO Debugger modifications, and related behaviors. Evaluate tool matches in context.

5. **Validate protections and determine recovery needs.**  
   Restore and validate Microsoft Defender protections, review backup and recovery integrity, and perform additional filesystem analysis to determine whether encryption occurred beyond what was visible in the reviewed telemetry.