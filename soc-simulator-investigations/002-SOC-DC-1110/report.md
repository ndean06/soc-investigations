# Incident Report

## 1. Report Title
**Unauthorized Domain Controller Compromise with Persistence and Sustained Outbound C2 Communication**

---

## 2. Date of Report
2026-02-15

---

## 3. Reported By
Nigel Dean (SOC Analyst)

---

## 4. Severity Level
**Critical**

---

## 5. Summary of Findings

Unauthorized RemoteInteractive logon activity was observed against the Domain Controller (`mts-dc.mts.local`) using the **Administrator** account. Following successful authentication, the attacker staged and executed malicious scripts and binaries from a user-writable directory.

Persistence was established via Startup folder shortcuts, triggering execution of `slhost.exe` and `TextIntelHost.exe`. Sustained outbound TCP communications were observed from the Domain Controller to known malicious infrastructure:

- `193.142.147.209`
- `193.142.146.230`

The IP addresses involved have a **100% abuse confidence rating** and are associated with known scanning and attack activity.

This incident represents a **full domain controller compromise with persistence and external command-and-control communications.**

---

## 6. Investigation Timeline

| Time (UTC) | Event Description |
|------------|------------------|
| 2026-01-11 | Earliest successful RemoteInteractive logon observed on contractor workstation (initial foothold suspected) |
| 2026-01-20 07:19 | RemoteInteractive Administrator logon to Domain Controller from `213.159.67.124` |
| 2026-01-20 07:20 | Batch file (`s.bat`) executed from `C:\Users\administrator\3D Objects` |
| 2026-01-20 07:20 | VBScript (`_.vbs`) created and executed via `cscript.exe` |
| 2026-01-20 07:20 | `slhost.exe` and `TextIntelHost.exe` dropped to disk |
| 2026-01-20 07:20 | Startup folder shortcuts (`banana_v001.lnk`, `deskt.lnk`) created for persistence |
| 2026-01-20 07:20–07:21 | Malicious binaries executed via `explorer.exe` and `cmd.exe` |
| 2026-01-20 07:29 onward | Repeated outbound TCP connections to malicious IPs |
| 2026-02-10 | Continued outbound communication confirms sustained activity |

---

## 7. Who, What, When, Where, Why, How

### Who
Administrator account on `mts-dc.mts.local` (Domain Controller)

### What
Unauthorized remote access, malicious script staging, execution of unsigned binaries, persistence via Startup folder, and sustained outbound TCP communications to known malicious IP infrastructure.

### When
Initial compromise observed on **2026-01-20 07:19 UTC**.  
Outbound communications continued through **2026-02-10**.

### Where
Domain Controller – `mts-dc.mts.local`

### Why
Likely credential compromise via brute force or password spray, followed by interactive RDP access. Activity pattern suggests persistence establishment and remote command-and-control communication.

### How
Initial access was achieved through repeated external authentication attempts consistent with a brute-force/password spray attack against the built-in Administrator account.

Telemetry shows multiple external Network and RemoteInteractive logons targeting the Domain Controller, followed by a successful Administrator authentication from external IP 213.159.67.124.

Immediately after successful authentication, the attacker initiated:

- Batch execution (s.bat)

- VBScript staging (_.vbs)

- Payload deployment (slhost.exe, TextIntelHost.exe)

- Startup folder persistence

- Sustained outbound TCP connections

Showing interactive access by the attacker via RDP

---

## 8. MITRE ATT&CK Techniques

| Tactic | Technique ID | Technique Name | Evidence Observed |
|--------|--------------|----------------|------------------|
| Initial Access | T1110.001 | Brute Force | Multiple authentication attempts targeting Administrator account resulting in successful logon |
| Initial Access | T1133 | External Remote Services | External RDP exposure used to access Domain Controller |
| Lateral Movement | T1021.001 | Remote Services: RDP | Successful RemoteInteractive logon to mts-dc.mts.local |
| Execution | T1059.003 | Command and Scripting Interpreter: Command Shell | `cmd.exe` executed `s.bat` staging file |
| Execution | T1059.005 | Command and Scripting Interpreter: Visual Basic | `cscript.exe` executed `_.vbs` to launch payload |
| Execution | T1105 | Ingress Tool Transfer | Malicious binaries (`slhost.exe`, `TextIntelHost.exe`) written to disk |
| Persistence | T1547.001 | Boot or Logon Autostart Execution: Startup Folder | Startup folder shortcuts (`banana_v001.lnk`, `deskt.lnk`) created for logon execution |
| Command & Control | T1071.001 | Application Layer Protocol (C2 over TCP) | Sustained outbound TCP connections to 193.142.147.209 and 193.142.146.230 |
 

---

## 9. Impact Assessment

The Domain Controller was compromised using valid Administrator credentials.

Persistence was successfully established, and malicious binaries executed from a user-writable directory.

Sustained outbound communications to known malicious infrastructure were observed over multiple days, indicating potential active C2 communication.

### Risk Level: Severe

- Domain Controller exposure  
- Privileged account compromise  
- Potential credential theft  
- Possible lateral movement  
- Possible data access or further environment compromise  

**Full domain trust integrity must be considered at risk.**

---

## 10. Recommendations / Next Steps

### Immediate Actions

- Immediately isolate `mts-dc.mts.local` from the network.
- Reset Administrator credentials and all privileged accounts.
- Invalidate Kerberos tickets and perform KRBTGT password reset (twice).
- Block outbound communications to:
  - `193.142.147.209`
  - `193.142.146.230`
  - `213.159.67.124`

### Further Investigation

- Perform full forensic acquisition of the Domain Controller.
- Conduct enterprise-wide hunt for:
  - `slhost.exe`
  - `TextIntelHost.exe`
  - `s.bat`
  - `_.vbs`
  - Startup folder persistence artifacts
- Review all RemoteInteractive logons environment-wide.
- Validate no additional DC modifications occurred.

### Hardening

- Disable direct external RDP exposure.
- Enforce MFA for privileged accounts.
- Review account lockout policy configuration.
- Enable enhanced conditional access monitoring.

---

## 11. Attachments / Evidence

- Defender Advanced Hunting queries  
- DeviceProcessEvents logs  
- DeviceFileEvents logs  
- DeviceLogonEvents logs  
- DeviceNetworkEvents logs  
- IP reputation screenshots (abuse database confirmation)  
- Hash values for `slhost.exe` and `TextIntelHost.exe`  
- Startup persistence artifacts  

---


