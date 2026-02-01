# Phishing Investigation Report  
**Case Type:** Email / Brand Impersonation  
**Classification:** Confirmed Phishing  

---

## Findings

- **Time:** 2025-03-19 15:03:51 EDT  
- **Host:** N/A (email-based activity)  
- **IOC – Domain:** `partners-uber[.]com`  
- **IOC – IP:** Unknown (email headers not provided)  
- **Possible Malware Family:** N/A (no payload observed)  
- **Filename:** N/A  
- **SHA256 Hash:** N/A  

---

## Investigation Summary

On **2025-03-19 at approximately 15:03:51 EDT**, an email claiming to represent *Uber partnerships* was received from  
`manager@partners-uber[.]com` using the display name **“Vanessa.”**

The message content was generic and lacked verifiable business details, links, or attachments.

Analysis determined that **partners-uber[.]com** is a **lookalike domain impersonating Uber**. WHOIS records showed the domain was **recently registered on 2025-03-05** and demonstrated **infrastructure instability**, including multiple IP address and hosting provider changes. The domain was also placed in a **clientHold** status, indicating registrar-level suspension.

Although no malicious payload was observed, these indicators align with **pretexting-style phishing**, where initial contact is intended to establish trust before follow-on malicious activity. Based on the evidence, this email was classified as a **confirmed phishing attempt via brand impersonation**.

---

## WHO / WHAT / WHEN / WHERE / WHY / HOW

- **WHO:** Unknown sender impersonating Uber using `partners-uber[.]com`  
- **WHAT:** Phishing email posing as a business partnership inquiry  
- **WHEN:** 2025-03-19 15:03:51 EDT  
- **WHERE:** Email delivery; no endpoint interaction observed  
- **WHY:** Establish trust for follow-on malicious activity (pretexting / BEC-style phishing)  
- **HOW:** Lookalike domain combined with generic outreach from a role-based sender  

---

## Analyst Next Steps

1. Block `partners-uber[.]com` at the email security gateway  
2. Perform email trace and scoping t
