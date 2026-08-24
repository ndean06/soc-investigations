# Investigation Report: KCD-Web - Nemesys Ransomware

## Findings

Time: 2026-04-01 18:14:42 UTC  
Host: KCD-Web  
Affected User: KCD-Web\receptionist  
Investigation: KCD-Web Nemesys Ransomware  
Suspicious Source IP: 141[.]98[.]83[.]86  
Possible Malware Family: Nemesys Ransomware  
Primary Payload: nemesys.exe  
Credential Access Tools: Mimikatz, LaZagne  
Defense Impairment Tool: DC.exe  
Discovery Tool: Everything.exe  
Tooling/Staging Directory: C:\Users\receptionist\Videos\automim1\  
Malware Staging Directory: C:\Users\receptionist\AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\  
Ransom Note: Info_to_decrypt_nemesys.txt  
Credential Output: Result.txt, Passwords.txt, NTLM.txt  
Primary Credential Dumping Commands: privilege::debug, sekurlsa::logonPasswords, token::elevate, lsadump::sam  
Final Determination: True Positive - Ransomware Activity Confirmed  

## Investigation

On 2026-04-01, suspicious authentication activity involving the KCD-Web\receptionist account was identified prior to the ransomware activity. Repeated failed authentication attempts associated with external IP 141[.]98[.]83[.]86 were reviewed as part of the suspected access path.

At 18:12:43 UTC, attacker tooling was staged under C:\Users\receptionist\Videos\automim1\. The toolkit contained credential-access utilities including Mimikatz and LaZagne. At 18:12:53 UTC, mimikatz.exe executed with privilege::debug, sekurlsa::logonPasswords, token::elevate, and lsadump::sam, confirming credential-dumping activity. Output was written to Result.txt and later processed by miparser.vbs. Passwords.txt and NTLM.txt were subsequently opened and reviewed.

At 18:14:42 UTC, nemesys.exe executed on KCD-Web. Supporting components were extracted using 7za.exe and staged within a randomly named AppData\Local directory. The staged tooling included DC.exe and Everything.exe. Registry Run persistence was established, and multiple Image File Execution Options (IFEO) Debugger registry modifications were observed.

At approximately 18:14:46 UTC, Everything.exe was launched with the -startup parameter, consistent with rapid file discovery and indexing. DC.exe performed Microsoft Defender impairment activity, including modification of the DisableAntiSpyware policy. DC.exe later executed as NT AUTHORITY\SYSTEM using the /SYS 1 parameter, confirming SYSTEM-level anti-security activity.

At 18:14:56 UTC, the ransomware-associated batch script 7ZSfx000.cmd executed and Info_to_decrypt_nemesys.txt was displayed through notepad.exe. This confirmed ransom-note deployment on KCD-Web.

File creation, deletion, and related endpoint telemetry were reviewed for evidence of mass encryption. Ransomware execution and preparation were confirmed; however, the available telemetry did not conclusively demonstrate widespread file encryption.

Broader scoping was performed using ransomware tooling, credential-dumping behavior, Defender impairment artifacts, ransom-note indicators, registry modifications, and related behavioral indicators. The malicious activity was primarily identified on KCD-Web, with no definitive evidence of ransomware propagation to additional hosts within the reviewed telemetry.

## 5W1H

WHO: The affected account was KCD-Web\receptionist. Suspicious authentication activity associated with external IP 141[.]98[.]83[.]86 was investigated as part of the suspected initial access path. The attacker subsequently used credential-access tooling including Mimikatz and LaZagne.

WHAT: Ransomware activity occurred on KCD-Web. The attacker staged the automim1 toolkit, dumped credentials, executed nemesys.exe, established Registry Run persistence, modified IFEO Debugger registry keys, impaired Microsoft Defender, performed file discovery, and displayed the Info_to_decrypt_nemesys.txt ransom note.

WHEN: Primary malicious tooling activity occurred on 2026-04-01 between approximately 18:12:43 UTC and 18:14:59 UTC. Mimikatz executed at 18:12:53 UTC and nemesys.exe executed at approximately 18:14:42 UTC.

WHERE: Activity occurred on KCD-Web under the receptionist account. Tooling was staged primarily within C:\Users\receptionist\Videos\automim1\ and C:\Users\receptionist\AppData\Local\1A0E4D54-2869-FFA5-37FD-1DF75A0FC5BA\.

WHY: The activity appears intended to obtain credentials, impair security controls, discover files, establish persistence, and execute ransomware against the affected system.

HOW: The suspected access path involved authentication activity against the receptionist account. After obtaining access, the attacker staged the automim1 toolkit, executed Mimikatz and LaZagne for credential access, launched nemesys.exe, extracted supporting tooling with 7za.exe, impaired Defender using DC.exe, and used Everything.exe for file discovery before deploying the ransom note. Widespread file encryption was not conclusively confirmed in the available telemetry.

## Recommendations

1. Isolate KCD-Web and preserve relevant endpoint, authentication, registry, and filesystem evidence before remediation or reimaging.

2. Reset the KCD-Web\receptionist account and rotate any administrator, service, application, or stored credentials that may have been exposed through Mimikatz or LaZagne.

3. Block 141[.]98[.]83[.]86 and review authentication telemetry for additional source IPs exhibiting the same failed-to-successful authentication pattern.

4. Hunt across the environment for nemesys.exe, Mimikatz, LaZagne, DC.exe, Everything.exe, Info_to_decrypt_nemesys.txt, DisableAntiSpyware modifications, IFEO Debugger modifications, and related behavioral indicators.

5. Restore and validate Microsoft Defender protections, review backup and recovery integrity, and perform additional filesystem analysis to determine whether encryption occurred beyond what was visible in the available endpoint telemetry.