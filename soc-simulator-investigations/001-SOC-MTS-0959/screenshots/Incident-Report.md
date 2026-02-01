# Incident Report – MTS-0959

---

## 1. Report Title
**Credential-Based Multi-Stage Intrusion with Malware and Email Rule Abuse (MTS-0959)**

---

## 2. Date of Report
2025-12-24

---

## 3. Reported By
Dean – SOC Analyst (Simulated Investigation)

---

## 4. Severity Level
**High**

*Reason:* Unauthorized access using valid credentials, malware execution, lateral movement, Linux root access, and cloud mailbox manipulation.

---

## 5. Summary of Findings

An external attacker used **compromised credentials** to gain remote access to Windows systems via RDP, move laterally across the environment, execute **Win32/Neshta.A** malware, access a Linux server as root, and create a malicious Outlook inbox rule consistent with **Business Email Compromise (BEC)** behavior.  
The activity was contained to a limited number of systems and accounts and did not spread further.

---

## 6. Investigation Timeline (UTC)

| Time (UTC) | Event Description |
|-----------|------------------|
| 21:17:06 | Successful RDP logon using compromised administrator account |
| 21:17:06 | RDP lateral movement to another Windows host detected |
| 21:18:54 | Anonymous cloud sign-in attempts observed |
| 21:21:00 | Win32/Neshta.A malware detected executing on Windows host |
| 21:21–22:17 | Multiple binaries infected by Neshta |
| 21:28:14 | SSH login to Linux server using root account |
| 21:28–21:35 | Linux system discovery and privilege-related activity |
| 21:35:00 | Malicious script downloaded via wget |
| 21:36:20 | Script deleted to evade detection |
| 21:41:29 | Malicious Outlook inbox rule created |

---

## 7. Who, What, When, Where, Why, How

**Who:**  
Compromised administrative accounts on Windows systems, root account on Linux server **MTS-Web**, and cloud mailbox **Zach Balrog**.

**What:**  
Unauthorized access, malware execution, lateral movement, Linux server compromise, and malicious email rule creation.

**When:**  
December 23, 2025 between **21:17 UTC and 21:41 UTC**.

**Where:**  
Windows endpoints, Linux server **MTS-Web**, and Microsoft Exchange Online.

**Why:**  
Likely intent was to maintain access, collect credentials and system information, and hide or manipulate emails, consistent with post-compromise and BEC activity.

**How:**  
The attacker used **valid stolen credentials**, native tools (RDP, SSH, wget, chmod), malware disguised as legitimate processes, and removed evidence to evade detection.

---

## 8. MITRE ATT&CK Techniques

| Tactic | Technique ID | Technique Name | Description |
|------|------------|----------------|-------------|
| Initial Access | T1021.001 | Remote Services: RDP | Attacker used RDP with compromised credentials to access Windows systems. |
| Execution | T1204 | User Execution | Malware executed while masquerading as legitimate OneDrive-related processes. |
| Execution | T1059.004 | Command and Scripting Interpreter: Unix Shell | Attacker executed shell commands on the Linux server after SSH login. |
| Discovery | T1082 | System Information Discovery | Attacker gathered OS and system details on the Linux host. |
| Discovery | T1083 | File and Directory Discovery | Attacker searched directories and files, including credential-related files. |
| Credential Access | T1552.001 | Credentials in Files | Attacker searched the filesystem for files containing passwords. |
| Defense Evasion | T1222.002 | File and Directory Permissions Modification | File permissions were modified to enable execution of malicious scripts. |
| Defense Evasion | T1070.004 | Indicator Removal: File Deletion | Malicious script was deleted after execution to remove evidence. |
| Command and Control | T1105 | Ingress Tool Transfer | Attacker downloaded an external script using wget. |
| Impact | TA0040 | Impact | Malware infected multiple binaries and altered system integrity. |
| Collection / Persistence | T1114.003 | Email Collection: Email Forwarding Rule | Malicious inbox rule created to manipulate email handling. |
| Defense Evasion | T1564.008 | Hide Artifacts: Email Hiding Rules | Inbox rule used to hide emails by moving them to Deleted Items. |

---

## 9. Impact Assessment

- Malware infected multiple Windows binaries.
- Linux system integrity was impacted through root-level actions.
- Cloud mailbox had a malicious inbox rule created, but no evidence of email forwarding or data exfiltration was observed.
- No additional systems or accounts were affected beyond the scoped assets.

---

## 10. Recommendations / Next Steps

- Reset passwords for all compromised accounts and enforce MFA.
- Isolate and remediate affected Windows and Linux systems; reimage if integrity cannot be confirmed.
- Block the following IP addresses:
  - 185.98.171.250
  - 185.98.171.249
  - 182.121.245.103
- Remove the malicious Outlook inbox rule and review all mailboxes for suspicious rules.
- Restrict RDP and SSH access to approved networks or VPNs only.
- Disable direct root SSH access where possible.

---

## 11. Attachments / Evidence

- Timeline.csv  
- IOCs.csv  
- Advanced Hunting queries  
- Defender XDR alerts and audit logs  
- File hash values  

---

## 12. Report Status
**Closed – Incident Contained**

---

## 13. Reviewed By
Self-reviewed (Simulated SOC Investigation)  
*For learning and skill development purposes*
