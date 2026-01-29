# Runbook: Phishing Email Triage

## Trigger

A phishing email is reported by a user, detected by the mail gateway, or identified during threat hunting.

## Scope

This runbook covers initial triage of a suspected phishing email. It does not cover full incident response for confirmed compromises (credential theft, malware execution).

---

## Steps

### 1. Preserve the Original Email

- Obtain the email in `.eml` format (full headers intact).
- Save to `samples/` with a descriptive name (e.g., `samples/2026-01-28-invoice-phish.eml`).
- Do **not** forward the email inline — this strips headers.

### 2. Extract and Analyze Headers

- Open the `.eml` in a text editor.
- Record the following:
  - **From** and **Reply-To** (do they match?)
  - **Received** chain (trace the first hop — where did the email actually originate?)
  - **SPF** result (pass / fail / softfail / none)
  - **DKIM** result (pass / fail / none)
  - **DMARC** result (pass / fail / none)
- Flag any mismatches or failures.

### 3. Extract and Inspect Links

- Search the email body for all `href=` values.
- For each link, compare the **visible text** to the **actual URL**.
- Check for:
  - URL shorteners (bit.ly, tinyurl, etc.)
  - Redirect chains
  - Look-alike domains (e.g., `paypa1.com` vs `paypal.com`)
- Submit suspicious URLs to [VirusTotal](https://www.virustotal.com) or [URLScan.io](https://urlscan.io). Do **not** click links directly.

### 4. Inspect Attachments (if any)

- Note the file type, name, and size.
- Compute the SHA-256 hash: `sha256sum <filename>`.
- Check the hash on VirusTotal.
- If the attachment is an archive (.zip, .rar), note whether it is password-protected.
- Do **not** open attachments on a production machine.

### 5. Write the Triage Report

- Use `reports/triage-template.md` to document findings.
- Include: indicators, IOCs, verdict, and recommended action.
- Save the completed report in `reports/` (e.g., `reports/2026-01-28-invoice-phish-triage.md`).

### 6. Take Action

| Verdict | Action |
|---------|--------|
| Phishing (confirmed) | Block sender/domain at gateway; quarantine from all mailboxes; notify affected users; escalate if credentials were entered or attachments opened. |
| Suspicious (inconclusive) | Escalate to Tier 2 or Incident Response for deeper analysis. |
| Not phishing | Close the ticket; optionally notify the reporter. |

### 7. Close

- Update the ticket/case with the triage report.
- Confirm that blocking rules are in place (if applicable).
- Document any lessons learned or detection gaps.

---

## Closure Criteria

- Email preserved as `.eml`.
- Headers and links analyzed and documented.
- Verdict rendered with supporting evidence.
- Recommended actions executed or escalated.
- Triage report saved in `reports/`.
