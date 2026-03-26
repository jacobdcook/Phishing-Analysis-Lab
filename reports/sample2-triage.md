# Phishing Triage Report

**Title:** Microsoft Account Credential Harvesting Campaign
**Date:** 2026-02-06
**Analyst:** Jacob Cook
**Sample filename:** samples/sample2.eml

---

## Suspicious Indicators

- [ ] From / Reply-To mismatch
- [x] Spoofed sender domain
- [x] SPF / DKIM / DMARC failures
- [x] Urgency or threatening language
- [x] Mismatched link text vs href
- [x] URL shorteners or redirect chains
- [ ] Suspicious attachment (type / name)
- [x] Other: X-Priority: 1 (high urgency flag), fake sign-in activity table with fabricated Russian IP, Contabo VPS hosting

## Extracted IOCs

| Type | Value | Notes |
|------|-------|-------|
| Sender address | noreply@microsoftonline-verify.com | Typosquat of legitimate `microsoftonline.com` |
| Domain | microsoftonline-verify.com | Not a Microsoft-owned domain. Registered to impersonate Microsoft login services |
| IP address | 178.238.225.91 | Contabo VPS (German budget hosting, commonly used in phishing infrastructure) |
| URL(s) | https://bit.ly/3vF9xKz | URL shortener hides real destination. "Review recent activity" button leads here |
| Subject line | [Action Required] Unusual sign-in activity on your account | Standard Microsoft credential phishing lure |
| Fabricated IP in body | 91.234.99.42 | Claimed Russian IP used as social engineering to create urgency |

## VirusTotal / URL Scanner Results

- URL checked: https://bit.ly/3vF9xKz
- **Detection ratio:** Checked against known phishing domain pattern
- **Notable detections:**
  - Domain `microsoftonline-verify.com` is a known typosquat pattern
  - Contabo IP 178.238.225.91 appears in threat intelligence feeds for phishing hosting
- **Assessment:** High-confidence phishing. Domain impersonates Microsoft, SPF/DKIM/DMARC all fail, URL shortener obscures destination

## Verdict

- [x] **Phishing**

**Reasoning:**
1. **Domain is a typosquat:** `microsoftonline-verify.com` mimics `microsoftonline.com` but is not Microsoft-owned
2. **All authentication fails:** SPF fail, DKIM fail, DMARC fail. Legitimate Microsoft emails pass all three
3. **URL shortener:** Microsoft never uses bit.ly links in security alerts. Legitimate alerts link to `account.microsoft.com`
4. **Contabo VPS hosting:** Budget hosting provider, not Microsoft infrastructure
5. **Social engineering:** Fabricated Russian IP login, X-Priority: 1, "[Action Required]" subject line all designed to create panic
6. **Template mimics real Microsoft alerts:** Layout, colors, and language closely match legitimate Microsoft security notifications

## Recommended Action

- [x] **Block sender / domain at mail gateway** — Block `microsoftonline-verify.com` and all subdomains
- [x] **Quarantine email across mailboxes** — Search for and remove all emails from this domain across all users
- [x] **User awareness notification** — Alert users: Microsoft security alerts never use URL shorteners or come from non-microsoft.com domains
- [ ] Escalate to Tier 2 / Incident Response (only if user clicked link or entered credentials)
- [ ] No action required (INCORRECT)

## Additional Notes

**Campaign characteristics:**
- This is a credential harvesting attack. The bit.ly link likely redirects to a fake Microsoft login page that captures usernames and passwords
- The fabricated "sign-in from Russia" table is designed to cause panic and bypass critical thinking
- X-Priority: 1 header is set to trigger urgency in email clients that display priority indicators

**Risk assessment:**
- If user clicks and enters credentials: immediate account compromise, potential lateral movement via OAuth tokens
- High risk: Microsoft credential phishing is the most common phishing type and has high success rates

**Long-term mitigation:**
- Block all emails from domains containing "microsoftonline" that are not `microsoftonline.com`
- Implement conditional access policies requiring MFA for all Microsoft 365 logins
- Train users: legitimate Microsoft alerts link to `account.microsoft.com`, never URL shorteners
