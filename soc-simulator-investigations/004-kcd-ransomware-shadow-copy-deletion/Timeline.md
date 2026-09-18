# Timeline: KCD Ransomware - Shadow Copy Deletion

| Time (UTC) | Event | Significance |
|---|---|---|
| **22:34:33** | `KCD-Web\administrator` successfully authenticated from `91[.]99[.]176[.]42` using NTLM | Confirmed suspicious access shortly before ransomware execution |
| **22:34:34** | Administrator reconnected to an RDP session from `91[.]99[.]176[.]42` | Supports hands-on-keyboard access before payload execution |
| **22:35:06** | `Stub.exe` created in `C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\` | Suspicious payload staged on the host |
| **22:35:31** | `Stub.exe` executed from the administrator Documents directory | Primary ransomware payload execution |
| **22:35:31** | `Stub.exe` launched `cmd.exe`, which executed `vssadmin Delete Shadows /All /Quiet` | Shadow copies deleted to prevent recovery |
| **22:35:32** | `wmic SHADOWCOPY /nointeractive` and `bcdedit` recovery changes executed | Additional ransomware recovery impairment |
| **22:35:33** | `Stub.exe` modified `HKCU\Software\Microsoft\Windows\CurrentVersion\Run\F56A2BB52AF4B409` | Confirmed Run-key persistence attempt |
| **22:35:33** | `Stub.exe` launched commands to delete `$RECYCLE.BIN` and `Recycler` across multiple drives | Supports destructive ransomware behavior |
| **22:35:40** | `Stub.exe` modified `C:\PerfLogs\DataRecovery.txt` | Ransom-note activity began |
| **22:35:47** | Defender detected `Ransom:Win64/WannaCrypt.PAGV!MTB` | Defender classified the activity as ransomware |
| **22:35:48** | `MsMpEng.exe` deleted the malicious Run-key value | Persistence artifact remediated |
| **22:35:50** | `DataRecovery.txt` created across multiple directories | Confirms ransom-note deployment |
| **22:35:50** | Defender quarantined the ransomware associated with `Stub.exe` | Malware remediation initiated |
| **22:36:12** | Encrypted filename artifacts containing `.[ransomhunters2026@gmail.com].fDKuxNIx` observed in the Windows Search index path | Supports ransomware impact; original encrypted file paths were not identified |
| **22:40:48** | `sihost.exe` deleted the RunNotification value related to the Run-key artifact | Follow-on cleanup of the shell notification artifact |

---
