\# Query Log: KCD Ransomware - Shadow Copy Deletion



\## Purpose



This file documents the main Splunk searches used during the investigation. Queries were sanitized for GitHub publication where appropriate. Replace placeholder values as needed when reusing the searches.



\---



\## Query 1: Initial Alert Review / Shadow Copy Deletion



\*\*Question:\*\* What activity occurred around the shadow copy deletion alert?



```spl

index=\* host="KCD-Web" earliest="07/05/2026:22:25:31" latest="07/05/2026:23:59:59"

("vssadmin" OR "Delete Shadows" OR "DataRecovery.txt" OR "Stub.exe")

| table \_time host User Image CommandLine ParentImage ParentCommandLine ProcessId ParentProcessId file\_name file\_path Message

| sort \_time

```



\*\*Finding:\*\* `Stub.exe` launched `cmd.exe`, which executed `vssadmin Delete Shadows /All /Quiet`.



\---



\## Query 2: Suspicious Access Review



\*\*Question:\*\* Was there suspicious access before ransomware execution?



```spl

index=\* earliest="07/05/2026:22:30:00" latest="07/05/2026:22:40:00"

("91.99.176.42" OR "91.238.181.47")

| table \_time host ComputerName user User Account\_Name EventCode src\_ip Workstation\_Name Logon\_Type Authentication\_Package

| sort \_time

```



\*\*Finding:\*\* `91.99.176.42` was tied to successful administrator authentication and RDP reconnect activity shortly before ransomware execution.



\---



\## Query 3: Payload Creation and Execution



\*\*Question:\*\* When was `Stub.exe` created and executed?



```spl

index=\* host="KCD-Web" earliest="07/05/2026:22:30:00" latest="07/05/2026:22:40:00"

("Stub.exe" OR "2147BE653CE551EC")

| table \_time host User EventCode Image CommandLine ParentImage ParentCommandLine TargetFilename file\_path file\_name Hashes

| sort \_time

```



\*\*Finding:\*\* `Stub.exe` was created and executed from the administrator Documents directory.



\---



\## Query 4: Recovery Impairment Commands



\*\*Question:\*\* What recovery-impairment commands were executed?



```spl

index=\* host="KCD-Web" earliest="07/05/2026:22:35:00" latest="07/05/2026:22:36:00"

("vssadmin" OR "wmic" OR "bcdedit" OR "Delete Shadows" OR "SHADOWCOPY" OR "recoveryenabled" OR "bootstatuspolicy")

| table \_time host User Image CommandLine ParentImage ParentCommandLine

| sort \_time

```



\*\*Finding:\*\* The ransomware chain executed shadow copy deletion and Windows recovery-impairment commands.



\---



\## Query 5: Persistence Review



\*\*Question:\*\* Did the payload attempt persistence?



```spl

index=\* host="KCD-Web" earliest="07/05/2026:22:34:00" latest="07/05/2026:22:45:00"

("CurrentVersion\\\\Run" OR "F56A2BB52AF4B409" OR "Stub.exe")

| table \_time host User EventCode Image TargetObject Details action Message

| sort \_time

```



\*\*Finding:\*\* `Stub.exe` created an HKCU Run key pointing back to the payload. Defender later removed the persistence artifact.



\---



\## Query 6: Ransom Note and Encrypted Filename Review



\*\*Question:\*\* Were ransom notes or encrypted filename artifacts observed?



```spl

index=\* host="KCD-Web" earliest="07/05/2026:22:35:00" latest="07/06/2026:00:00:00"

("DataRecovery.txt" OR "ransomhunters2026@gmail.com" OR "fDKuxNIx")

| eval observed\_path=coalesce(TargetFilename,file\_path)

| table \_time host User EventCode Image observed\_path file\_name action

| sort \_time

```



\*\*Finding:\*\* `DataRecovery.txt` ransom notes and encrypted filename artifacts were observed.



\---



\## Query 7: Brute-Force Source Review



\*\*Question:\*\* Did `91.238.181.47` successfully authenticate?



```spl

index=\* earliest="07/01/2026:00:00:00" latest="07/18/2026:00:00:00"

"91.238.181.47"

| stats count min(\_time) as firstSeen max(\_time) as lastSeen values(user) as Usernames values(Account\_Name) as Accounts values(dest\_port) as Dest\_Ports values(action) as Actions by host EventCode

| convert ctime(firstSeen) ctime(lastSeen)

| sort host EventCode

```



\*\*Finding:\*\* `91.238.181.47` showed high-volume RDP failed-authentication activity but no confirmed successful logon.



\---



\## Query 8: Collection / Staging Review



\*\*Question:\*\* Was there evidence of archive creation or file staging?



```spl

index=\* host="KCD-Web" earliest="07/05/2026:22:30:00" latest="07/06/2026:00:00:00"

("7z" OR "7za" OR "rar" OR "winrar" OR "Compress-Archive" OR ".zip" OR ".rar" OR ".7z" OR "robocopy" OR "xcopy" OR "rclone" OR "mega" OR "winscp" OR "ftp" OR "curl" OR "Invoke-WebRequest" OR "iwr" OR "bitsadmin")

| table \_time host User EventCode process\_name Image CommandLine ParentImage ParentCommandLine file\_path TargetFilename action

| sort \_time

```



\*\*Finding:\*\* No attacker-controlled archive creation or staging activity was confirmed in the reviewed telemetry.



\---



\## Query 9: Outbound Network Review



\*\*Question:\*\* Was suspicious outbound traffic observed from the impacted host?



```spl

index=\* host="KCD-Web" earliest="07/05/2026:22:30:00" latest="07/06/2026:00:00:00" EventCode=3

| where src\_ip="172.16.1.7"

| where NOT cidrmatch("10.0.0.0/8", dest\_ip)

| where NOT cidrmatch("172.16.0.0/12", dest\_ip)

| where NOT cidrmatch("192.168.0.0/16", dest\_ip)

| stats count min(\_time) as firstSeen max(\_time) as lastSeen values(dest\_port) as dest\_ports values(action) as actions by process\_name Image dest\_ip

| convert ctime(firstSeen) ctime(lastSeen)

| sort - count

```



\*\*Finding:\*\* No suspicious outbound traffic from `Stub.exe`, scripting tools, archive tools, or upload utilities was confirmed.



\---



\## Query 10: Remote Access Tool Review



\*\*Question:\*\* Were common RMM or secondary remote access tools observed?



```spl

index=\* host="KCD-Web" earliest="07/01/2026:00:00:00" latest="07/06/2026:00:00:00"

(EventCode=1 OR EventCode=4688 OR EventCode=7045)

("ScreenConnect" OR "ConnectWise" OR "Atera" OR "TeamViewer" OR "AnyDesk" OR "VNC" OR "TightVNC" OR "UltraVNC" OR "RealVNC" OR "Splashtop" OR "RustDesk" OR "MeshAgent" OR "MeshCentral" OR "LogMeIn" OR "GoToAssist" OR "ZohoAssist")

| table \_time host User EventCode Image CommandLine ParentImage ParentCommandLine ServiceName ServiceFileName

| sort \_time

```



\*\*Finding:\*\* No common RMM or secondary remote access tool activity was confirmed.



\---



\## Query Log Summary



| Area | Result |

|---|---|

| Initial Access | Suspicious administrator/RDP access from `91\[.]99\[.]176\[.]42` was confirmed. |

| Execution | `Stub.exe` executed from the administrator Documents directory. |

| Recovery Impairment | `vssadmin`, `wmic`, and `bcdedit` recovery-disabling commands were observed. |

| Persistence | HKCU Run key persistence was attempted and later remediated by Defender. |

| Impact | Ransom notes and encrypted filename artifacts were observed. |

| Collection / Exfiltration | Reviewed but not confirmed in available telemetry. |

| Brute Force | `91\[.]238\[.]181\[.]47` showed failed RDP activity, but no confirmed successful logon. |

