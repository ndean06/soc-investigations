# PCAP Investigations
This folder contains network traffic investigations and related artifacts.

# PCAP Investigations

This repository contains **network-based security investigations** conducted using packet capture (PCAP) analysis.  
Each case demonstrates how malicious activity can be identified, scoped, and explained **using only network traffic**, without reliance on SIEM or endpoint telemetry.

The focus is on **analyst thinking**, evidence-driven conclusions, and clear incident reporting.

---

## 🔍 What You’ll Find Here
- Phishing-based initial access investigations
- Malware delivery and staging analysis
- DNS, HTTP, and TLS traffic correlation
- Encrypted command-and-control (C2) beaconing detection
- IOC extraction and reporting

---

## 🛠️ Tools & Techniques
- Wireshark & Tshark
- HTTP / DNS / TLS analysis
- Beaconing pattern detection
- Timeline reconstruction
- MITRE ATT&CK mapping

---

## 📂 Investigations

| Investigation | Description |
|--------------|-------------|
| [**TA577 PCAP Investigation**](./TA577-PCAP-Investigation/) | Phishing link → malicious ZIP delivery → encrypted TLS beaconing |

> Click an investigation to view the full README and analyst walkthrough.

---

## 🧠 Analyst Approach
Each investigation follows a consistent workflow:
1. Validate PCAP scope and timing
2. Identify suspicious protocols and top talkers
3. Analyze DNS, HTTP, and TLS traffic
4. Correlate activity into a timeline
5. Map behavior to MITRE ATT&CK
6. Provide actionable recommendations

---

## 🎯 Purpose
This repository is intended to showcase:
- Practical SOC analyst skills
- Real-world investigation methodology
- Clear communication of technical findings

---

## 🔗 Contact
If you’re a recruiter, hiring manager, or fellow defender and would like to discuss these investigations, feel free to connect with me on LinkedIn.

