# IOC Tracker: KCD Ransomware - Shadow Copy Deletion

| IOC Type | Value | Description | Status |
|---|---|---|---|
| Host | KCD-Web | Primary impacted host where ransomware activity occurred. | Confirmed |
| User Account | KCD-Web\administrator | Account used for suspicious successful access and ransomware execution activity. | Confirmed |
| Source IP | 91.99.176.42 | Confirmed suspicious access IP tied to successful administrator logon and RDP reconnect activity. | Confirmed |
| Source IP | 91.238.181.47 | High-volume RDP failed authentication source. No successful logon confirmed. | Suspicious |
| File Name | Stub.exe | Primary suspicious executable associated with ransomware activity. | Confirmed |
| File Path | C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe | Full path of the ransomware payload. | Confirmed |
| SHA256 | 1D7323941B44E77F22DD93701CAE781D330AA26881993B9D2307F1C783AC7CD7 | SHA256 hash tied to Stub.exe. | Confirmed |
| SHA1 | 40BF54C808F2A2F2AEB000480A36CC117839FA4B | SHA1 hash tied to Stub.exe. | Confirmed |
| MD5 | 829BF5971E4808CB5D8ED52BBA328B59 | MD5 hash tied to Stub.exe. | Confirmed |
| Command Line | vssadmin Delete Shadows /All /Quiet | Shadow copy deletion command executed by the ransomware chain. | Confirmed |
| Command Line | wmic SHADOWCOPY /nointeractive | Additional shadow copy/recovery-related command. | Confirmed |
| Command Line | bcdedit /set {default} recoveryenabled No | Command used to disable Windows recovery. | Confirmed |
| Command Line | bcdedit /set {default} bootstatuspolicy ignoreallfailures | Command used to weaken boot failure recovery behavior. | Confirmed |
| Registry Key | HKCU\Software\Microsoft\Windows\CurrentVersion\Run\F56A2BB52AF4B409 | Run key persistence artifact created by Stub.exe. | Confirmed / Remediated |
| Ransom Note | DataRecovery.txt | Ransom note created across multiple directories. | Confirmed |
| Ransom Email | ransomhunters2026@gmail.com | Email address observed in encrypted filename artifacts. | Confirmed |
| File Pattern | .[ransomhunters2026@gmail.com].fDKuxNIx | Ransomware-style encrypted filename pattern observed in Windows Search index artifacts. | Confirmed |
| Defender Detection | Ransom:Win64/WannaCrypt.PAGV!MTB | Defender detection associated with Stub.exe and Run key persistence artifact. | Confirmed / Quarantined |

## Notes

- `91.99.176.42` is the confirmed suspicious access IP.
- `91.238.181.47` generated high-volume RDP failed authentication activity but was not confirmed to have successfully authenticated.
- Exact original encrypted file paths were not identified in the reviewed telemetry.
- Collection and exfiltration were reviewed but not confirmed.