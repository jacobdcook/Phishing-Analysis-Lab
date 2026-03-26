# Phishing Triage Report

**Title:** Fake PayPal Invoice / Tech Support Scam
**Date:** 2026-02-10
**Analyst:** Jacob Cook
**Sample filename:** samples/sample3.eml

---

## Suspicious Indicators

- [x] From / Reply-To mismatch
- [x] Spoofed sender domain
- [x] SPF / DKIM / DMARC failures
- [x] Urgency or threatening language
- [x] Mismatched link text vs href
- [x] URL shorteners or redirect chains
- [ ] Suspicious attachment (type / name)
- [x] Other: Homoglyph in domain (capital I instead of lowercase l in "paypaI"), fake invoice for $849.99, phone number for "dispute" is attacker-controlled

## Extracted IOCs

| Type | Value | Notes |
|------|-------|-------|
| Sender address | accounting@paypaI-resolution.com | Homoglyph: capital "I" instead of lowercase "l" in "paypal" |
| Reply-To address | dispute-center@paypaI-resolution.com | Different from sender, attacker-controlled dispute address |
| Domain | paypaI-resolution.com | Typosquat with homoglyph. Not PayPal-owned |
| IP address | 87.120.37.118 | HostKey Netherlands VPS |
| URL(s) | https://tinyurl.com/y6b3kw8e | "Dispute This Transaction" button leads to URL shortener |
| Phone number | +1 (888) 431-2847 | Attacker-controlled phone line for voice phishing (vishing) |
| Fake invoice | PP-2026-8834291 | Fabricated invoice number for "Geek Squad Subscription Services" |

## VirusTotal / URL Scanner Results

- URL checked: https://tinyurl.com/y6b3kw8e
- **Detection ratio:** URL shortener obscures final destination
- **Notable detections:**
  - Domain `paypaI-resolution.com` uses homoglyph substitution (I vs l), a known evasion technique
  - HostKey Netherlands IP appears in threat intelligence feeds
- **Assessment:** High-confidence phishing. Combines fake invoice social engineering with both URL phishing and vishing (voice phishing) vectors

## Verdict

- [x] **Phishing**

**Reasoning:**
1. **Homoglyph domain:** `paypaI-resolution.com` uses a capital I instead of lowercase l to impersonate PayPal. This is a deliberate evasion technique
2. **From/Reply-To mismatch:** Sender is `accounting@` but replies go to `dispute-center@`, indicating attacker wants to intercept responses
3. **SPF/DKIM/DMARC all fail:** Legitimate PayPal emails pass authentication
4. **Dual attack vector:** Both a phishing URL and a phone number are provided. The phone number is for vishing (voice phishing) where a fake agent collects payment or credential info
5. **Fake urgency:** "$849.99 payment processed" for a service the recipient did not purchase creates panic
6. **Geek Squad scam pattern:** This is a well-documented scam type where attackers send fake invoices for tech support services

## Recommended Action

- [x] **Block sender / domain at mail gateway** — Block `paypaI-resolution.com` and all subdomains
- [x] **Quarantine email across mailboxes** — Remove all emails from this domain
- [x] **User awareness notification** — Alert users: PayPal never sends invoices from non-paypal.com domains. Never call phone numbers from suspicious emails
- [ ] Escalate to Tier 2 / Incident Response (only if user called the number or entered credentials)
- [ ] No action required (INCORRECT)

## Additional Notes

**Campaign characteristics:**
- This is a hybrid phishing/vishing attack combining email lure with a phone number callback
- The "Geek Squad" fake invoice is one of the most common phishing lures of 2025-2026
- Homoglyph substitution (I vs l) is designed to evade both human visual inspection and basic string-matching filters

**Risk assessment:**
- If user clicks URL: credential harvesting page or malware download
- If user calls phone number: social engineering for credit card info, remote desktop access, or gift card payment
- Medium-high risk: fake invoice scams have high conversion rates among non-technical users

**Long-term mitigation:**
- Implement homoglyph detection rules at the mail gateway (flag domains containing mixed-case substitutions for common brands)
- Block all emails with "PayPal" in the display name that don't originate from paypal.com
- Train users: check the actual sender domain character by character for brand impersonation attempts
