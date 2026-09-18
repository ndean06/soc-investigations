# Timeline: KCD Ransomware - Shadow Copy Deletion

---

## Timeline

| Time (UTC) | Event | Significance |
|---|---|---|
| **22:34:33** | `KCD-Web\administrator` successfully authenticated from `91[.]99[.]176[.]42` using NTLM | Suspicious external access established |
| **22:35:06** | `Stub.exe` created in the administrator Documents directory | Ransomware payload staged on the endpoint |
| **22:35:31** | `Stub.exe` executed | Ransomware execution began |
| **22:35:31** | `cmd.exe` launched `vssadmin Delete Shadows /All /Quiet` | Shadow copies targeted to inhibit recovery |
| **22:35:31+** | `wmic SHADOWCOPY /nointeractive` and `bcdedit` activity observed | Additional recovery-impairment activity |
| **22:35:50** | `DataRecovery.txt` ransom notes created across multiple directories | Ransomware impact became visible |
| **22:35:50+** | Files with `.[ransomhunters2026[at]gmail[.]com].fDKuxNIx` naming pattern observed | Encrypted filename artifacts supported ransomware impact |
| **22:36:13** | Malicious activity continued within the identified incident window | End of primary observed activity window |
| **Post-Execution** | Microsoft Defender detected `Ransom:Win64/WannaCrypt.PAGV!MTB` | Malware identified by endpoint protection |
| **Post-Detection** | Defender quarantined the associated threat | Endpoint remediation action performed |

---