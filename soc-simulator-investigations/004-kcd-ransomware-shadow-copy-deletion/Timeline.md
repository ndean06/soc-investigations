# Timeline: KCD Ransomware - Shadow Copy Deletion



| Timestamp UTC | Event Type | User | Process / IOC | Description | Significance |

|---|---|---|---|---|---|

| 2026-07-05 22:34:33 | Successful Logon | KCD-Web\\administrator | 91.99.176.42 | Administrator account successfully authenticated from external IP 91.99.176.42 using NTLM. | Confirmed suspicious access shortly before ransomware execution. |

| 2026-07-05 22:34:34 | RDP Session Reconnect | KCD-Web\\administrator | 91.99.176.42 | Administrator reconnected to an RDP session from 91.99.176.42. | Supports hands-on-keyboard access before payload execution. |

| 2026-07-05 22:35:06 | File Creation | KCD-Web\\administrator | Stub.exe | Stub.exe was created in `C:\\Users\\administrator\\Documents\\2147BE653CE551EC\\x64-Release\\`. | Suspicious payload staged on the host. |

| 2026-07-05 22:35:31 | Process Execution | KCD-Web\\administrator | Stub.exe | Stub.exe executed from the administrator Documents directory. | Primary ransomware payload execution. |

| 2026-07-05 22:35:31 | Recovery Impairment | KCD-Web\\administrator | vssadmin.exe | Stub.exe launched cmd.exe, which executed `vssadmin Delete Shadows /All /Quiet`. | Shadow copies were deleted to prevent recovery. |

| 2026-07-05 22:35:32 | Recovery Impairment | KCD-Web\\administrator | wmic / bcdedit | Stub.exe launched additional recovery-related commands including `wmic SHADOWCOPY /nointeractive` and `bcdedit` recovery changes. | Additional ransomware recovery impairment behavior. |

| 2026-07-05 22:35:33 | Persistence | KCD-Web\\administrator | HKCU Run Key | Stub.exe modified `HKCU\\Software\\Microsoft\\Windows\\CurrentVersion\\Run\\F56A2BB52AF4B409` to point back to Stub.exe. | Confirmed Run key persistence attempt. |

| 2026-07-05 22:35:33 | Impact / Cleanup | KCD-Web\\administrator | Recycle Bin Deletion | Stub.exe launched commands to delete `$RECYCLE.BIN` and `Recycler` directories across multiple drive letters. | Supports destructive ransomware behavior. |

| 2026-07-05 22:35:40 | Ransom Note Modification | KCD-Web\\administrator | DataRecovery.txt | Stub.exe modified `C:\\PerfLogs\\DataRecovery.txt`. | Ransom note activity began shortly after payload execution. |

| 2026-07-05 22:35:47 | Defender Detection | KCD-Web\\administrator | Ransom:Win64/WannaCrypt.PAGV!MTB | Microsoft Defender detected ransomware tied to Stub.exe and the Run key artifact. | Confirms Defender classified the activity as ransomware. |

| 2026-07-05 22:35:48 | Remediation | NT AUTHORITY\\SYSTEM | MsMpEng.exe | Defender deleted the Run key value created by Stub.exe. | Persistence artifact was remediated. |

| 2026-07-05 22:35:50 | Ransom Note Creation | KCD-Web\\administrator | DataRecovery.txt | Stub.exe created DataRecovery.txt ransom notes across multiple user and system-accessible directories. | Confirms ransom note deployment. |

| 2026-07-05 22:35:50 | Defender Quarantine | NT AUTHORITY\\SYSTEM | Ransom:Win64/WannaCrypt.PAGV!MTB | Defender quarantined the ransomware tied to Stub.exe. | Malware remediation was initiated. |

| 2026-07-05 22:36:12 | Encrypted Filename Artifacts | KCD-Web\\administrator | .\[ransomhunters2026@gmail.com].fDKuxNIx | Encrypted filename artifacts were observed in the Windows Search index path. | Supports ransomware impact, but original encrypted file paths were not identified. |

| 2026-07-05 22:40:48 | Cleanup | KCD-Web\\administrator | RunNotification Artifact | sihost.exe deleted the RunNotification value related to the Run key artifact. | Follow-on cleanup of shell notification artifact. |

