# Phishing Triage Report

**Title:** OneCasino Promotional Spam / Phishing Campaign
**Date:** 2026-01-29
**Analyst:** Jacob Cook
**Sample filename:** samples/sample1.eml

---

## Suspicious Indicators

- [x] From / Reply-To mismatch
- [x] Spoofed sender domain
- [x] SPF / DKIM / DMARC failures
- [x] Urgency or threatening language
- [x] Mismatched link text vs href
- [x] URL shorteners or redirect chains
- [ ] Suspicious attachment (type / name)
- [x] Other: Spam markers in subject (emoji, random hash), OVH VPS hosting, auto-replied flag

## Extracted IOCs

| Type | Value | Notes |
|------|-------|-------|
| Sender address | 132784@534617.vav.proo55.us.com | Random generic subdomain, not legitimate casino |
| Domain | vav.proo55.us.com | OVH VPS (bullet-proof hosting for spam/phishing campaigns) |
| IP address | 141.95.0.46 | OVH netblock (cheap hosting favored by spammers) |
| URL(s) | https://tinyurl.com/mrymsuhv | URL shortener hides real destination — classic phishing tactic |
| Subject line | Welcome gift inside: 50 spins waiting for you💰.944175#. | Spam signature: emoji, urgency, random hash suffix |
| Return-Path | bounce@vav.proo55.us.com | Bounce/mailer-daemon address from attacker infrastructure |

## VirusTotal / URL Scanner Results

- URL checked: https://tinyurl.com/mrymsuhv
- **Detection ratio:** 2 / 94 security vendors flagged this URL as malicious
- **Notable detections:**
  - **Phishing Database:** Phishing (flagged by phishing-specific database)
  - **SafeToOpen:** Phishing (flagged by SafeToOpen threat intelligence)
- **Content type:** text/html; charset=utf-8 (web page, not executable — likely credential harvester or malware redirect)
- **Status:** 200 (OK — page loads successfully, suggesting active phishing infrastructure)
- **Last analysis:** a moment ago (page recently checked — still active)

## Verdict

- [x] **Phishing**

**Reasoning:**
1. **Sender domain is not legitimate:** `534617.vav.proo55.us.com` is a random subdomain, not `onecasino.com` or any official casino domain
2. **Hosted on bullet-proof VPS:** OVH IP 141.95.0.46 is commonly used for phishing campaigns and spam
3. **URL shortener hides destination:** Using tinyurl to obscure the real link is a primary phishing indicator — legitimate companies link directly
4. **VirusTotal confirms malicious:** 2 vendors flagged as "Phishing" (Phishing Database and SafeToOpen) — even low detection ratio indicates active phishing
5. **Every link goes to the same shortener:** Even "Unsubscribe" and "Terms" links point to the same tinyurl, confirming this is not a legitimate email service
6. **SPF/DKIM pass for wrong domain:** The email passes SPF/DKIM checks but for `534617.vav.proo55.us.com`, not a real casino — passing authentication does NOT mean legitimate

## Recommended Action

- [x] **Block sender / domain at mail gateway** — Add `vav.proo55.us.com` and `534617.vav.proo55.us.com` to blocklist
- [x] **Quarantine email across mailboxes** — Remove from all user inboxes; check for similar emails from same domain
- [x] **User awareness notification** — Alert users: casino promotional emails with URL shorteners are phishing; never click shortened links
- [ ] Escalate to Tier 2 / Incident Response (only if user clicked the link or entered credentials)
- [ ] No action required (INCORRECT)

## Additional Notes

**Campaign characteristics:**
- Auto-Submitted flag indicates this is part of an automated bulk mailing
- The email uses social engineering: "Free spins, no deposit" is a classic lure for non-tech-savvy users
- OVH VPS + random subdomain + tinyurl is a well-known phishing infrastructure pattern

**Risk assessment:**
- If user clicks: likely lands on credential harvesting page (Casino login clone) or malware redirect
- Detection evasion: Low detection ratio (2/94) suggests this shortener URL is new or not yet widely reported — URL shorteners slow down detection

**Long-term mitigation:**
- Block OVH VPS ranges that are flagged for phishing (may be overly broad)
- Implement URL shortener policy: block tinyurl.com, bit.ly, etc. at gateway level
- User training: teach recognition of legitimate casino domains vs generic sender addresses
