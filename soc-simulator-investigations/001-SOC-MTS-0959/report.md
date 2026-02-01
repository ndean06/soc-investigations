# Investigation Report – Incident MTS-0959

## Findings

**Time:**  
2025-12-23 21:17:06 UTC – 21:41:29 UTC  

**Hosts:**  
- Windows endpoints: `mts-contractorpc1`, `desktop-1`  
- Linux server: `MTS-Web`  

**IOC IPs:**  
- 185.98.171.250  
- 185.98.171.249  
- 182.121.245.103  

**Possible Malware Family:**  
- Win32/Neshta.A  

**Filenames:**  
- FileCoAuth.exe  
- OneDriveLauncher.exe  
- OneDriveStandaloneUpdater.exe  
- /root/bin.sh  

**File Hash (SHA1):**  
- 5857a7dd621c4c2ebb0b53ebc915d409f70d39f  

---

## Investigation

On **2025-12-23 at 21:17:06 UTC**, an external attacker successfully logged into a Windows system using compromised administrator credentials via **RDP** from IP address **185.98.171.250**. Shortly after, the attacker used RDP to move laterally to another Windows host using a different administrative account.

At **21:21:00 UTC**, **Win32/Neshta.A** malware was observed executing on the Windows system. The malware infected multiple binaries and masqueraded as legitimate OneDrive-related processes.

At **21:28:14 UTC**, the attacker logged into the Linux server **MTS-Web** via **SSH** as the root user from IP address **185.98.171.249**. Following access, the attacker performed system discovery, searched for credential-related files, downloaded an external script using `wget`, made it executable, and later deleted it to evade detection.

At **21:41:29 UTC**, a malicious Outlook inbox rule was created in the cloud mailbox **Zach Balrog** from IP **185.98.171.249**, consistent with **Business Email Compromise (BEC)** behavior. The rule was used to hide emails containing sensitive financial keywords.

Based on the available evidence, this activity represents a **confirmed multi-stage compromise** involving endpoint access, malware execution, server compromise, and cloud mailbox manipulation. Scoping confirmed that the activity did not extend beyond the identified systems and mailbox.

---

## WHO / WHAT / WHEN / WHERE / WHY / HOW

**WHO**  
Windows endpoints `mts-contractorpc1` and `desktop-1`, Linux server `MTS-Web`, and cloud mailbox `Zach Balrog` accessed using compromised credentials.

**WHAT**  
Unauthorized remote access, lateral movement, malware execution, Linux system compromise, and creation of a malicious Outlook inbox rule.

**WHEN**  
Activity occurred on **2025-12-23 between 21:17 UTC and 21:41 UTC**.

**WHERE**  
The activity took place across Windows systems, a Linux server, and Microsoft Exchange Online, originating from external IP addresses **185.98.171.249** and **185.98.171.250**.

**WHY**  
The attacker’s intent appears to be maintaining access, collecting system and credential information, staging malicious tools, and hiding or manipulating emails.

**HOW**  
The attacker used **valid stolen credentials**, native tools (RDP, SSH, wget, chmod), malware disguised as legitimate processes, and deleted artifacts to reduce detection.

---

## Recommendations

1. **Reset compromised accounts and enable MFA**  
   Reset passwords for all affected accounts and enforce multi-factor authentication, especially for administrative and cloud accounts.

2. **Isolate and remediate affected systems**  
   Isolate the impacted Windows and Linux systems. Reimage systems if integrity cannot be confidently verified.

3. **Block malicious infrastructure**  
   Block the following IP addresses across firewall, endpoint, and cloud controls:  
   - 185.98.171.250  
   - 185.98.171.249  
   - 182.121.245.103  

4. **Review and remove malicious email rules**  
   Remove the malicious Outlook inbox rule and review all mailboxes for additional hidden or suspicious rules.

5. **Restrict remote access**  
   Limit RDP and SSH access to approved networks or VPNs only and disable direct root SSH access where possible.
