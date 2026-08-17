# Password Spray to Bind-Style Backdoor – Defender XDR Investigation

## Overview

This project documents a simulated SOC investigation within Microsoft Defender XDR that began with a high-severity password spray alert and escalated into confirmed host compromise involving a bind-style backdoor.

The investigation demonstrates alert validation, process reconstruction, defense evasion detection, and network-based command-and-control analysis.

---

## Environment

- Platform: Microsoft Defender XDR
- Host: mts-contractorpc1
- Account Targeted: administrator
- Investigation Type: Tier 3 Deep-Dive (Lab Simulation)

---

## What Triggered the Investigation

A Defender alert identified multiple failed authentication attempts followed by a successful login to the administrator account.

Subsequent analysis confirmed:

- RemoteInteractive (RDP) session
- Batch file execution
- VBScript payload staging
- Creation of malicious binaries
- Defender exclusion modification

---

## Key Findings

- `slhost.exe` and `textintelhost.exe` were dropped and executed.
- Windows Defender exclusions were added to bypass protection.
- A TCP listener was created on port 8080 bound to all interfaces.
- Sustained inbound connections from multiple external public IP addresses were observed.
- Compromise was confirmed.

---

## Techniques Observed

- Password Spraying
- Command Execution via cmd.exe and cscript.exe
- Registry Modification
- Security Tool Evasion
- Bind-Style Command & Control over TCP 8080

---

## Skills Demonstrated

- Defender XDR Advanced Hunting (KQL)
- Process tree analysis
- Network telemetry investigation
- C2 identification (bind listener model)
- SOC case workflow handling
- Structured documentation for escalation

---

## Project Structure

This repository contains:

- Incident summary
- Timeline reconstruction
- Technical deep-dive analysis
- MITRE ATT&CK mapping
- Investigation notes and screenshots

---

## Final Determination

Confirmed host compromise with exposed backdoor listener behavior.

This project demonstrates full lifecycle SOC investigation from alert validation through command-and-control identification.

---

*Lab Simulation – Portfolio Project*