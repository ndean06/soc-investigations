# Phishing Investigation  Uber Brand Impersonation

## Summary
- Investigated a phishing email impersonating Uber partnerships
- Confirmed malicious via lookalike domain and WHOIS analysis
- No payload observed; pretexting-style phishing

## Key Indicators
- Lookalike domain: partners-uber[.]com
- Recently registered domain
- Registrar suspension (clientHold)
- Generic partnership lure

## Final Verdict
**Confirmed Phishing – Brand Impersonation**  
MITRE ATT&CK: T1566

## Investigation Report
- 📄 [SOC Investigation Report](report.pdf)

## Confidence & Limitations
This assessment is based on available email content and WHOIS data. Full email headers and user interaction telemetry were not available; therefore, while the activity is confidently classified as phishing via brand impersonation, follow-on activity cannot be confirmed.

## Analyst Next Actions
- Block domain
- Perform email trace and scoping 
- Add IOCs to tracking
- Monitor for follow-up attempts

## Takeaways
- Lookalike domains and WHOIS data can confirm phishing even without a payload.
- Pretexting emails often precede later malicious activity.
- Infrastructure analysis is key when header data is unavailable.