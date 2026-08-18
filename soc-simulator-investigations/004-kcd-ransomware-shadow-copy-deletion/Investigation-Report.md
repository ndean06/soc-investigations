# Investigation Report: KCD Ransomware - Shadow Copy Deletion

## Findings

Time: 2026-07-05 22:35:31 UTC  
Host: KCD-Web  
Host IP: 172.16.1.7  
Affected User: KCD-Web\administrator  
Alert Name: KCD - Endpoint - Shadow Copy Deletion  
Suspicious Source IP: 91.99.176.42  
Additional Suspicious IP: 91.238.181.47  
Possible Malware Family: Ransomware / Defender Detection: Ransom:Win64/WannaCrypt.PAGV!MTB  
Filename: Stub.exe  
File Path: C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe  
SHA256 Hash: 1D7323941B44E77F22DD93701CAE781D330AA26881993B9D2307F1C783AC7CD7  
SHA1 Hash: 40BF54C808F2A2F2AEB000480A36CC117839FA4B  
MD5 Hash: 829BF5971E4808CB5D8ED52BBA328B59  
Ransom Note: DataRecovery.txt  
Ransom Email: ransomhunters2026@gmail.com  
Encrypted Filename Pattern: .[ransomhunters2026@gmail.com].fDKuxNIx  
Primary Command Observed: vssadmin Delete Shadows /All /Quiet  
Final Determination: True Positive - Ransomware Activity Confirmed  

## Investigation

On 2026-07-05 at 22:34:33 UTC, the KCD-Web\administrator account successfully authenticated to KCD-Web from external IP 91.99.176.42 using NTLM authentication. Shortly after, at 22:35:06 UTC, a suspicious executable named Stub.exe was created in C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\.

At 22:35:31 UTC, Stub.exe executed from the administrator Documents directory and launched cmd.exe, which executed vssadmin Delete Shadows /All /Quiet to delete shadow copies. Additional recovery-impairment activity was also observed, including wmic SHADOWCOPY /nointeractive and bcdedit commands to weaken or disable Windows recovery options.

At 22:35:50 UTC, Stub.exe created DataRecovery.txt ransom notes across multiple user and system-accessible directories on KCD-Web. Encrypted filename artifacts containing [ransomhunters2026@gmail.com].fDKuxNIx were also observed shortly after execution, supporting ransomware impact. Microsoft Defender later detected the activity as Ransom:Win64/WannaCrypt.PAGV!MTB and quarantined the threat.

Collection and exfiltration were reviewed through searches for archive/staging behavior, common exfiltration tools, suspicious outbound traffic, and common remote access tools. No confirmed exfiltration, secondary remote access tooling, or exact original encrypted file inventory was identified in the reviewed telemetry.

## 5W1H

WHO: The affected account was KCD-Web\administrator. Successful suspicious access came from 91.99.176.42. IP 91.238.181.47 showed failed RDP attempts but no confirmed successful logon.

WHAT: Ransomware activity occurred on KCD-Web. Stub.exe executed, impaired recovery, attempted Run key persistence, created DataRecovery.txt ransom notes, and produced encrypted filename artifacts.

WHEN: Key activity occurred on 2026-07-05 between 22:34:33 UTC and 22:36:13 UTC. Stub.exe executed at 22:35:31 UTC.

WHERE: Activity occurred on KCD-Web. The payload was located at C:\Users\administrator\Documents\2147BE653CE551EC\x64-Release\Stub.exe.

WHY: The activity appears intended to impact/encrypt files and prevent recovery.

HOW: The attacker likely used valid administrator/RDP access from 91.99.176.42. Stub.exe then launched vssadmin, wmic, and bcdedit commands. Defender later detected and quarantined the malware as Ransom:Win64/WannaCrypt.PAGV!MTB.

## Recommendations

1. Coordinate emergency containment of KCD-Web. If possible, isolate immediately; if not, restrict access to required services only and restore from a known-good backup as soon as possible.

2. Reset KCD-Web\administrator and rotate any local admin, service, application, backup, or stored credentials exposed on KCD-Web.

3. Block 91.99.176.42 and 91.238.181.47 at the perimeter. Treat 91.99.176.42 as the confirmed suspicious access IP.

4. Restrict RDP access to KCD-Web using VPN, MFA, and source IP allow-listing.

5. Confirm Defender remediation, backup integrity, and business impact. Ransomware impact was confirmed, but exfiltration and exact encrypted files were not confirmed.