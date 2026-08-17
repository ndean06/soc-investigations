\# IOC Tracker: KCD Ransomware - Shadow Copy Deletion



\## Sanitization Note



This IOC tracker has been sanitized for portfolio publication. Public IP addresses and email-style indicators have been defanged. No credentials, session tokens, private portal URLs, or sensitive business data are included.



\---



\## Confirmed Indicators



| IOC Type | Value | Status |

|---|---|---|

| Host | `KCD-Web` | Confirmed |

| User Account | `KCD-Web\\administrator` | Confirmed |

| Source IP | `91\[.]99\[.]176\[.]42` | Confirmed Suspicious Access |

| File Name | `Stub.exe` | Confirmed |

| SHA256 | `1D7323941B44E77F22DD93701CAE781D330AA26881993B9D2307F1C783AC7CD7` | Confirmed |

| SHA1 | `40BF54C808F2A2F2AEB000480A36CC117839FA4B` | Confirmed |

| MD5 | `829BF5971E4808CB5D8ED52BBA328B59` | Confirmed |

| Ransom Note | `DataRecovery.txt` | Confirmed |

| Defender Detection | `Ransom:Win64/WannaCrypt.PAGV!MTB` | Confirmed / Quarantined |



\---



\## Suspicious Access Indicators



| IOC Type | Value | Notes |

|---|---|---|

| Source IP | `91\[.]99\[.]176\[.]42` | Successful administrator logon and RDP reconnect activity occurred shortly before ransomware execution. |

| Source IP | `91\[.]238\[.]181\[.]47` | High-volume RDP failed authentication source. No successful logon was confirmed. |



\---



\## Payload Path



```text

C:\\Users\\administrator\\Documents\\2147BE653CE551EC\\x64-Release\\Stub.exe

```



\---



\## Recovery Impairment Commands



```text

vssadmin Delete Shadows /All /Quiet

wmic SHADOWCOPY /nointeractive

bcdedit /set {default} recoveryenabled No

bcdedit /set {default} bootstatuspolicy ignoreallfailures

```



\---



\## Persistence Artifact



```text

HKCU\\Software\\Microsoft\\Windows\\CurrentVersion\\Run\\F56A2BB52AF4B409

```



\### Registry Value Data



```text

C:\\Users\\administrator\\Documents\\2147BE653CE551EC\\x64-Release\\Stub.exe

```



\---



\## Ransomware Filename Pattern



```text

.\[ransomhunters2026\[at]gmail\[.]com].fDKuxNIx

```



\### Ransom Email



```text

ransomhunters2026\[at]gmail\[.]com

```



\---



\## Reviewed but Not Confirmed



| Area Reviewed | Result |

|---|---|

| Collection / Staging | No attacker-controlled archive creation or staging activity was confirmed. |

| Exfiltration Tools | No confirmed rclone, MEGA, WinSCP, or FileZilla execution was observed before July 6. |

| Suspicious Outbound Traffic | No suspicious outbound traffic from Stub.exe, scripting tools, archive tools, or upload utilities was confirmed. |

| Remote Access Tools | No ScreenConnect, Atera, TeamViewer, AnyDesk, VNC, RustDesk, or similar RMM tool activity was confirmed. |

| Exact Encrypted File Inventory | Encrypted filename artifacts were observed, but original encrypted file paths were not identified in reviewed telemetry. |



\---



\## Notes



\- `91\[.]99\[.]176\[.]42` is treated as the confirmed suspicious access IP.

\- `91\[.]238\[.]181\[.]47` is treated as a suspicious brute-force/password-guessing source, not the confirmed compromise path.

\- Ransomware impact was confirmed through recovery impairment commands, ransom note deployment, Defender detection, and encrypted filename artifacts.

\- Exfiltration was reviewed but not confirmed in the available telemetry.

