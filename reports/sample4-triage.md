# Phishing Triage Report

**Title:** Fake DocuSign / HR Document Credential Harvesting
**Date:** 2026-02-19
**Analyst:** Jacob Cook
**Sample filename:** samples/sample4.eml

---

## Suspicious Indicators

- [ ] From / Reply-To mismatch
- [x] Spoofed sender domain
- [x] SPF / DKIM / DMARC failures
- [x] Urgency or threatening language
- [x] Mismatched link text vs href
- [ ] URL shorteners or redirect chains
- [ ] Suspicious attachment (type / name)
- [x] Other: Fake HR document with due date pressure, link goes directly to attacker domain instead of docusign.net, Vultr VPS hosting, "do not share" language discourages verification

## Extracted IOCs

| Type | Value | Notes |
|------|-------|-------|
| Sender address | hr-department@docusign-secure.net | Not a DocuSign-owned domain |
| Domain | docusign-secure.net | Typosquat of legitimate `docusign.net`. Registered on Vultr VPS |
| IP address | 149.28.144.73 | Vultr VPS (cloud hosting, commonly used in phishing) |
| URL(s) | https://docusign-secure.net/sign/8f3a2b91c4d7 | Direct link to attacker infrastructure, not actual DocuSign |
| Fake HR contact | sarah.mitchell@company.com | Fabricated contact to add legitimacy |

## VirusTotal / URL Scanner Results

- URL checked: https://docusign-secure.net/sign/8f3a2b91c4d7
- **Detection ratio:** Domain registered recently, limited coverage in threat feeds
- **Notable detections:**
  - Domain `docusign-secure.net` is not owned by DocuSign Inc.
  - Vultr IP 149.28.144.73 is a VPS, not DocuSign infrastructure
- **Assessment:** High-confidence phishing. Impersonates DocuSign to harvest corporate credentials through a fake document signing workflow

## Verdict

- [x] **Phishing**

**Reasoning:**
1. **Fake domain:** `docusign-secure.net` is not DocuSign. Legitimate DocuSign emails come from `@docusign.net` or `@docusign.com`
2. **SPF/DKIM/DMARC all fail:** Legitimate DocuSign notifications pass all authentication checks
3. **Link goes to attacker domain:** The "REVIEW DOCUMENT" button links to `docusign-secure.net`, not `docusign.net`
4. **HR pretext with deadline:** "Updated Employee Handbook" with a 2-day deadline creates urgency. Employees expect HR documents and are less likely to question them
5. **Anti-sharing language:** "Do not share this email" discourages the recipient from forwarding to IT for verification
6. **Vultr VPS:** DocuSign runs on enterprise infrastructure, not budget cloud VPS

## Recommended Action

- [x] **Block sender / domain at mail gateway** — Block `docusign-secure.net` and all subdomains
- [x] **Quarantine email across mailboxes** — Remove all emails from this domain
- [x] **User awareness notification** — Alert users: DocuSign emails only come from `@docusign.net` or `@docusign.com`. Always verify the URL before clicking
- [ ] Escalate to Tier 2 / Incident Response (only if user clicked and entered credentials)
- [ ] No action required (INCORRECT)

## Additional Notes

**Campaign characteristics:**
- DocuSign phishing is one of the most effective BEC (Business Email Compromise) lures because employees are conditioned to sign HR documents quickly
- The "due date" creates urgency without being overtly threatening
- "Do not share this email" is a social engineering tactic to prevent the recipient from asking IT or colleagues to verify

**Risk assessment:**
- If user clicks: likely lands on a fake Microsoft/Google login page (credential harvesting). DocuSign phishing typically harvests SSO credentials, not DocuSign-specific passwords
- High risk in corporate environments where employees regularly sign documents via DocuSign

**Long-term mitigation:**
- Create mail flow rules that flag DocuSign-themed emails not originating from docusign.net or docusign.com
- Train users: always check the domain in the "REVIEW DOCUMENT" button by hovering before clicking
- Implement FIDO2/WebAuthn MFA to make harvested passwords useless even if users fall for phishing
