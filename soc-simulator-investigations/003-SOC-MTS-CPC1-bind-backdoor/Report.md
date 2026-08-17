# Incident Report

---

## 1. Report Title:
Password Spray Leading to Bind-Style Backdoor on mts-contractorpc1

---

## 2. Date of Report:
2026-02-19

---

## 3. Reported By:
Dean (SOC Analyst – Lab Simulation)

---

## 4. Severity Level:
Critical

---

## 5. Summary of Findings:

A password spray attack successfully authenticated to the `administrator` account on host **mts-contractorpc1**. 

Following successful login, malicious scripts were executed which:

- Staged and executed two payloads (`slhost.exe`, `textintelhost.exe`)
- Modified Windows Defender settings to add exclusions
- Hid dropped files using attribute modification
- Created a TCP listener on port 8080 bound to all interfaces

The listener accepted sustained inbound connections from multiple external public IP addresses over several hours.

Host compromise was confirmed.

---

## 6. Investigation Timeline:

| Time (UTC) | Event Description |
|------------|------------------|
| 2026-02-17 22:24 | Password spray alert triggered |
| 2026-02-17 22:24 | Successful login to administrator confirmed |
| 2026-02-18 08:09 | RemoteInteractive (RDP) session established |
| 2026-02-18 08:10 | s.bat executed via cmd.exe |
| 2026-02-18 08:10 | Defender exclusion added |
| 2026-02-18 08:10 | slhost.exe and textintelhost.exe created |
| 2026-02-18 08:10 | textintelhost.exe opened listener on TCP 8080 |
| 2026-02-18 08:27 | First inbound external connection accepted |
| 2026-02-19 00:03 | Listener still active with inbound traffic |

---

## 7. Who, What, When, Where, Why, How

**Who:**  
administrator account on mts-contractorpc1

**What:**  
Password spray attack resulting in execution of malicious payloads and exposed backdoor listener.

**When:**  
Initial access on 2026-02-17 22:24 UTC  
Post-compromise activity on 2026-02-18

**Where:**  
Host: mts-contractorpc1  
Local IP: 192.168.10.11  
Listener Port: TCP 8080

**Why:**  
Likely unauthorized remote control and persistent access.

**How:**  
Password spray → Successful admin authentication → RDP access → Batch + VBScript execution → Defender tampering → Bind-style TCP listener exposed externally.

---

## 8. MITRE ATT&CK Techniques:

- T1110 – Password Spraying
- T1059 – Command Interpreter
- T1112 – Modify Registry
- T1562 – Impair Defenses
- T1571 – Non-Standard Port (8080)
- T1133 – External Remote Services
- T1021 – Remote Services

---

## 9. Impact Assessment:

- Administrator account compromised.
- Malicious binaries executed.
- Security controls modified.
- Publicly exposed TCP listener enabled sustained inbound external connections.
- High risk of remote control and lateral movement if not contained.

No confirmed data exfiltration observed in this investigation window.

---

## 10. Recommendations / Next Steps:

- Immediately isolate affected host.
- Reset administrator credentials.
- Remove Defender exclusions.
- Block inbound TCP 8080 at firewall.
- Review external firewall/NAT exposure.
- Reimage system and perform full forensic review.
- Hunt for similar activity across environment.

---

## 11. Attachments / Evidence:

- Microsoft Defender incident screenshots
- Advanced Hunting query results
- Network telemetry showing InboundConnectionAccepted events
- Process execution chain evidence
- File creation logs for slhost.exe and textintelhost.exe
