# SOC Simulator Investigations

This folder contains simulated SOC alert investigations and related artifacts.

Each investigation is written to show how an analyst reviews an alert, builds a timeline, maps activity to MITRE ATT&CK, documents evidence, and reaches a final determination.

---

## What You’ll Find Here

- SOC alert investigations
- Analyst walkthroughs
- Final investigation reports
- Timeline summaries
- IOC trackers
- MITRE ATT&CK mappings
- Splunk and KQL query logs
- Screenshot-supported evidence

---

## Tools & Techniques

| Area | Tools / Techniques |
|---|---|
| SIEM / Detection Platforms | Splunk, Microsoft Defender XDR, Microsoft Sentinel |
| Query Languages | SPL, KQL |
| Endpoint Telemetry | Sysmon, Windows Event Logs, Defender telemetry |
| Detection Review | Scheduled alerts, endpoint detections, authentication events |
| Investigation Method | Alert triage, process chain analysis, timeline building |
| Threat Mapping | MITRE ATT&CK |
| Reporting | IOC tracking, 5W1H, final determination, recommendations |

---

## Investigations

| Investigation Description | Summary |
|---|---|
| [KCD Ransomware: Shadow Copy Deletion](./004-kcd-ransomware-shadow-copy-deletion) | RDP access → ransomware payload execution → shadow copy deletion → recovery impairment → ransom note deployment |

> Click an investigation to view the full README, report, timeline, IOCs, MITRE mapping, query log, and analyst walkthrough.

---

## Analyst Approach

Each investigation follows a consistent workflow:

1. Review the alert and initial evidence.
2. Identify the affected host, user, and process.
3. Build the process chain.
4. Pivot backward to determine possible access path.
5. Pivot forward to validate impact.
6. Review persistence, recovery impairment, and lateral movement indicators.
7. Check for collection and exfiltration evidence.
8. Map activity to MITRE ATT&CK.
9. Document IOCs, timeline, findings, and recommendations.
10. Write a final analyst conclusion.

---

## Purpose

This repository is intended to showcase:

- SOC alert triage methodology
- Practical Splunk and KQL investigation skills
- Windows endpoint investigation experience
- Ransomware investigation workflow
- MITRE ATT&CK mapping
- Clear analyst reporting
- Portfolio-ready cybersecurity documentation

---

## Contact

- GitHub: [ndean06](https://github.com/ndean06)
- LinkedIn: [Nigel Dean](https://www.linkedin.com/in/nigeldeanmed)