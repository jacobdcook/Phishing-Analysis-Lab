# Phishing Triage Report

**Title:** Fake IT Helpdesk / Mailbox Storage Credential Harvesting
**Date:** 2026-03-07
**Analyst:** Jacob Cook
**Sample filename:** samples/sample5.eml

---

## Suspicious Indicators

- [ ] From / Reply-To mismatch
- [x] Spoofed sender domain
- [x] SPF / DKIM / DMARC failures
- [x] Urgency or threatening language
- [x] Mismatched link text vs href
- [ ] URL shorteners or redirect chains
- [ ] Suspicious attachment (type / name)
- [x] Other: HTTP (not HTTPS) link with query parameters exposing user targeting, Verizon residential IP (not enterprise mail server), fake ticket number, 24-hour deadline, no DKIM signature at all

## Extracted IOCs

| Type | Value | Notes |
|------|-------|-------|
| Sender address | helpdesk@it-servicedesk-portal.com | Generic IT helpdesk impersonation domain |
| Domain | it-servicedesk-portal.com | Not associated with any legitimate organization |
| IP address | 72.184.231.94 | Verizon FiOS residential IP (Boston area), not a mail server |
| URL(s) | http://it-servicedesk-portal.com/verify?uid=jcook&token=a8f2b3c1d4e5 | HTTP (not HTTPS), query params expose targeted user ID |
| Fake ticket | INC-40291 | Fabricated ServiceNow-style incident number to add legitimacy |

## VirusTotal / URL Scanner Results

- URL checked: http://it-servicedesk-portal.com/verify?uid=jcook&token=a8f2b3c1d4e5
- **Detection ratio:** Domain is generic, limited threat intelligence coverage
- **Notable detections:**
  - HTTP-only (no TLS) is a strong phishing indicator for any login or verification page
  - Residential IP hosting a "service desk" is abnormal
  - Query parameter `uid=jcook` indicates targeted (spear) phishing, not bulk campaign
- **Assessment:** High-confidence phishing. Impersonates internal IT to harvest email credentials via fake storage upgrade flow

## Verdict

- [x] **Phishing**

**Reasoning:**
1. **Generic impersonation domain:** `it-servicedesk-portal.com` mimics internal IT but is not associated with any real organization
2. **SPF fail, no DKIM:** Legitimate IT service desks use authenticated corporate email infrastructure
3. **Residential IP as mail server:** 72.184.231.94 is a Verizon FiOS residential connection in Boston, not enterprise mail infrastructure. This is a compromised home device or VPS
4. **HTTP link (no HTTPS):** No legitimate IT department would send users to an HTTP verification page in 2026. This is a credential harvesting page with no TLS
5. **Targeted spear phishing:** The URL contains `uid=jcook`, indicating this email was crafted specifically for the recipient, not a mass campaign
6. **24-hour deadline:** "Action required within 24 hours" and "Critical" status creates panic to bypass verification instincts
7. **Fake ServiceNow ticket:** `INC-40291` mimics real ticketing systems to add legitimacy

## Recommended Action

- [x] **Block sender / domain at mail gateway** — Block `it-servicedesk-portal.com`
- [x] **Quarantine email across mailboxes** — Remove from all inboxes, search for variants
- [x] **User awareness notification** — Alert users: internal IT will never send you to an external domain to "verify" or "upgrade" your mailbox
- [x] **Escalate to Tier 2 / Incident Response** — The targeted `uid=jcook` parameter indicates spear phishing; investigate whether the attacker has additional intel on the target
- [ ] No action required (INCORRECT)

## Additional Notes

**Campaign characteristics:**
- This is a spear phishing attack, not a bulk campaign. The personalized URL parameter and specific mailbox capacity numbers indicate the attacker researched the target
- IT helpdesk impersonation is effective because users trust internal IT communications and are conditioned to follow IT instructions
- The "mailbox full" pretext is a classic social engineering technique that has been in active use since the early 2000s but remains effective

**Risk assessment:**
- If user clicks: HTTP page will harvest email credentials (username/password). No TLS means credentials are also exposed to network observers
- High risk: if this is spear phishing, the attacker likely has a specific objective beyond mass credential collection (BEC, lateral movement, data access)
- The residential IP suggests the attacker is using a compromised device as a relay, making IP-based blocking less useful long-term

**Long-term mitigation:**
- Block all inbound email from residential IP ranges (or at minimum flag for review)
- Implement URL rewriting at the mail gateway to flag HTTP-only links in emails
- Create detection rules for emails containing "mailbox", "storage", "quota" from external domains
- Train users: your IT department will never ask you to click an external link to manage your mailbox
