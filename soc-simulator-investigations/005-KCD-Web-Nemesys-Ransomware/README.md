# KCD-Web Nemesys Ransomware Investigation

## Overview

This investigation analyzed ransomware activity on `KCD-Web` involving
credential theft, malicious tooling, defense impairment, persistence,
and execution of `nemesys.exe`. The investigation worked backward from
the ransom note and ransomware activity to identify the supporting
toolchain, credential-access activity, suspected access path, and scope
of the compromise.

## Case Summary

  Field                     Details
  ------------------------- -----------------------------------------------
  Client                    Kerning City Dental
  Alert / Investigation     KCD-Web Nemesys Ransomware
  Host                      KCD-Web
  User                      KCD-Web`\receptionist`{=tex}
  Primary Suspicious IP     141.98.83.86
  Primary Payload           nemesys.exe
  Credential Access Tools   Mimikatz, LaZagne
  Defense Impairment Tool   DC.exe
  Discovery Tool            Everything.exe
  Final Determination       True Positive - Ransomware Activity Confirmed

## Key Findings

-   Suspicious authentication activity was identified prior to the
    ransomware activity, including repeated failed authentication
    attempts associated with the suspected access path.
-   The `automim1` toolkit was staged under the receptionist user's
    `Videos` directory and contained credential-access tooling including
    Mimikatz and LaZagne.
-   Mimikatz executed with `privilege::debug`,
    `sekurlsa::logonPasswords`, `token::elevate`, and `lsadump::sam`,
    confirming credential-dumping activity.
-   Credential output was written to `Result.txt`, parsed with
    `miparser.vbs`, and subsequently reviewed through `Passwords.txt`
    and `NTLM.txt`.
-   `nemesys.exe` executed and staged additional components within a
    randomly named `AppData\Local` directory.
-   `7za.exe` was used to extract supporting ransomware components
    including `DC.exe` and `Everything.exe`.
-   Registry Run persistence and multiple IFEO Debugger registry
    modifications were observed.
-   `DC.exe` performed Microsoft Defender impairment activity and later
    executed under `NT AUTHORITY\SYSTEM`.
-   `Everything.exe -startup` was executed, consistent with rapid file
    discovery/indexing activity.
-   `Info_to_decrypt_nemesys.txt` was displayed on the affected host,
    confirming ransom-note deployment.
-   Available telemetry confirmed ransomware execution and preparation;
    however, widespread file encryption was not conclusively
    demonstrated.
-   Broader scoping primarily identified the malicious toolchain on
    `KCD-Web`, with no definitive evidence of ransomware propagation to
    additional hosts in the reviewed telemetry.

## MITRE ATT&CK Mapping

  ----------------------------------------------------------------------------
  Tactic                  Technique               Observed Behavior
  ----------------------- ----------------------- ----------------------------
  Initial Access          Brute Force             Repeated authentication
                                                  failures were investigated
                                                  as part of the suspected
                                                  access path.

  Credential Access       OS Credential Dumping   Mimikatz executed
                                                  `sekurlsa::logonPasswords`
                                                  and `lsadump::sam`; LaZagne
                                                  activity was also
                                                  identified.

  Execution               Command and Scripting   `cmd.exe`, PowerShell,
                          Interpreter             `wscript.exe`, and
                                                  `7ZSfx000.cmd` supported
                                                  execution of the malicious
                                                  toolchain.

  Persistence             Registry Run Keys /     `nemesys.exe` established
                          Startup Folder          Registry Run persistence.

  Privilege Escalation    Elevated Execution      `DC.exe` transitioned into
                                                  `NT AUTHORITY\SYSTEM`
                                                  execution context.

  Defense Impairment      Impair Defenses         Defender policy changes,
                                                  `DisableAntiSpyware`,
                                                  `DC.exe`, recovery-related
                                                  changes, and IFEO
                                                  modifications were observed.

  Discovery               File and Directory      `Everything.exe -startup`
                          Discovery               was executed during
                                                  ransomware operations.

  Impact                  Data Encrypted for      Ransomware execution and
                          Impact                  ransom-note deployment were
                                                  confirmed; widespread
                                                  encryption could not be
                                                  conclusively validated from
                                                  available telemetry.
  ----------------------------------------------------------------------------

## Investigation Artifacts

  ------------------------------------------------------------------------
  Artifact                             Description
  ------------------------------------ -----------------------------------
  [Investigation                       Final SOC-style investigation
  Report](./Investigation-Report.md)   report

  [Walkthrough](./Walkthrough.md)      Analyst investigation process and
                                       pivots

  [Timeline](./Timeline.md)            Key event timeline

  [IOC Tracker](./IOC-Tracker.md)      Indicators of compromise and
                                       investigation artifacts

  [MITRE Mapping](./MITRE-Mapping.md)  ATT&CK tactic and technique mapping

  [Query Log](./Query-Log.md)          Splunk queries used during the
                                       investigation
  ------------------------------------------------------------------------

## Skills Demonstrated

-   Ransomware investigation
-   Splunk log analysis
-   Windows Security event analysis
-   Sysmon process, registry, and file telemetry analysis
-   Credential-dumping investigation
-   Parent/child process analysis
-   Malware staging and execution-chain reconstruction
-   Authentication analysis
-   Timeline reconstruction
-   IOC and behavior-based scoping
-   MITRE ATT&CK mapping
-   Impact validation
-   Enterprise scoping
-   SOC incident reporting
