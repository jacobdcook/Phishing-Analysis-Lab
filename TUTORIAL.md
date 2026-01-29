# Phishing Analysis Lab — Step-by-Step Tutorial

This tutorial walks you through analyzing a phishing email from start to finish: obtaining a sample, inspecting headers, extracting links, and writing a triage report. By the end you will have hands-on evidence of your analysis skills.

> **Safety first:** Never click links or open attachments from suspected phishing emails on a production machine. Use a sandboxed VM or isolated lab environment when handling live samples.

---

## Step 1 — Get Sample Phishing Emails

### Where to find samples

| Source | Notes |
|--------|-------|
| [PhishTank](https://phishtank.org) | Community-verified phishing URLs. Browse the feed and save page source. |
| [ThePhish](https://github.com/emalderson/ThePhish) | Open-source phishing analysis tool with sample datasets. |
| Your own mail client | If you receive obvious spam/phishing, save it properly (see below). |
| Lab-generated | Craft a mock phishing email yourself for practice (never send it). |

### How to save an email as `.eml`

- **Thunderbird:** Right-click the message → *Save As* → choose `.eml` format.
- **Outlook (desktop):** Drag the message to a folder on disk — it saves as `.msg`. Convert to `.eml` with a tool, or use *File → Save As → Outlook Message Format - Unicode (.msg)* then convert.
- **Gmail (web):** Open the message → three-dot menu → *Show original* → *Download Original*. This gives you a `.eml` (MIME) file.
- **Any web client:** View the full message source/headers, copy the entire text, paste into a new file, and save as `sample.eml`.

### Where to put the file

Save the `.eml` file(s) into the `samples/` directory of this project:

```
Phishing-Analysis-Lab/
└── samples/
    ├── sample1.eml
    └── sample2.eml
```

---

## Step 2 — Map Email Headers

Open one of your `.eml` files in a text editor (VS Code, Notepad++, Sublime Text, nano, etc.). The raw email starts with a block of headers before the body.

### Headers to find and map

| Header | What it tells you |
|--------|-------------------|
| **From** | The displayed sender address. Can be spoofed easily. |
| **Reply-To** | Where replies actually go. If different from `From`, that is suspicious. |
| **Received** | Chain of mail servers the message passed through. The **bottom-most** `Received` header is the first hop (closest to the true origin). |
| **Authentication-Results** | Contains SPF, DKIM, and DMARC verdicts from the receiving mail server. |
| **SPF** (in Authentication-Results) | Did the sending IP match the domain's SPF record? `pass` = legitimate server; `fail`/`softfail` = possible spoof. |
| **DKIM** (in Authentication-Results) | Was the email cryptographically signed by the claimed domain? `pass` = signature valid; `fail`/`none` = unsigned or tampered. |
| **DMARC** (in Authentication-Results) | Overall policy result combining SPF and DKIM alignment. `pass` = aligned; `fail` = likely spoof. |

### What to note

For each header, write a one-line observation. Example:

```
From: billing@paypa1.com          → look-alike domain (paypa1 vs paypal)
Reply-To: attacker@freemail.com   → different from From — suspicious
SPF: softfail                     → sending IP not authorized
DKIM: none                        → message not signed
DMARC: fail                       → fails alignment checks
```

### Screenshot

Capture the `.eml` open in your text editor with the headers visible. Optionally add short labels or annotations to highlight the key headers.

**Save as:** `screenshots/01-eml-headers-in-editor.png`

![Email headers in editor](screenshots/01-eml-headers-in-editor.png)

---

## Step 3 — Extract and Inspect Links

### 3a. Extract links from the email body

Search the `.eml` body for all hyperlinks. You can:

- Use `grep` in a terminal:
  ```bash
  grep -oP 'href="[^"]*"' samples/sample1.eml
  ```
- Or visually scan the HTML body in your text editor for `<a href="...">` tags.

For each link, compare:

| Visible text (what the user sees) | Actual `href` (where it really goes) |
|-----------------------------------|--------------------------------------|
| "Click here to verify your account" | `http://evil-site.com/phish/login.php` |
| "https://www.paypal.com/account" | `http://paypa1-secure.xyz/steal.html` |

Red flags:
- Visible text looks like a legitimate URL, but `href` points elsewhere.
- URL shorteners (bit.ly, tinyurl, etc.) hiding the real destination.
- Redirect chains through multiple domains.
- Look-alike / typosquatting domains.

### Screenshot

Capture the link text vs actual `href` comparison. This can be:
- The raw HTML in your editor showing `<a href="evil-url">legit-looking-text</a>`.
- A browser hover showing the real URL in the status bar.
- A side-by-side comparison you annotate.

**Save as:** `screenshots/02-links-vs-href.png`

![Link text vs href](screenshots/02-links-vs-href.png)

### 3b. Check URLs safely

Pick one extracted URL and submit it to [VirusTotal](https://www.virustotal.com) (URL tab) or [URLScan.io](https://urlscan.io).

**Do not click the link directly.** Copy-paste the URL into the scanner.

Review the results:
- How many engines flagged it as malicious?
- What categories were assigned (phishing, malware, spam)?
- Is the domain newly registered?

### Screenshot

Capture the VirusTotal (or URLScan) result page showing the URL and detection results.

**Save as:** `screenshots/03-virustotal-result.png`

![VirusTotal result](screenshots/03-virustotal-result.png)

---

## Step 4 — Write a One-Page Triage Report

Open the triage template at `reports/triage-template.md`. Copy it to a new file for this sample:

```bash
cp reports/triage-template.md reports/sample1-triage.md
```

Fill in every section:

1. **Suspicious indicators** — check off every indicator you found (header mismatches, link mismatches, SPF/DKIM/DMARC failures, urgency language, etc.).
2. **Extracted IOCs** — list sender addresses, URLs, domains, and attachment hashes.
3. **VirusTotal results** — paste in the detection ratio and notable findings.
4. **Verdict** — Phishing, Not phishing, or Inconclusive.
5. **Recommended action** — what a SOC analyst would do next (block sender, quarantine, notify users, escalate).

### Screenshot

Capture the completed triage report in your editor or exported as a PDF.

**Save as:** `screenshots/04-triage-report-sample.png`

![Triage report sample](screenshots/04-triage-report-sample.png)

---

## Step 5 — Repeat and Keep Notes

Practice makes the skill stick. Repeat Steps 1–4 with at least 2–3 more phishing samples. For each sample:

1. Save the `.eml` in `samples/`.
2. Analyze headers and links.
3. Write a triage report in `reports/`.

As you build experience, you will get faster at spotting indicators and writing concise triage reports. Keep all your reports in this repository as evidence of your growing analysis skills.

### Optional: Use the runbook

Follow `reports/runbook-phishing-triage.md` as a checklist for each sample to build muscle memory for the SOC triage workflow.

---

## Summary

By completing this lab you have demonstrated:

- How to safely obtain and handle phishing email samples.
- How to read and interpret email headers (From, Reply-To, Received, SPF, DKIM, DMARC).
- How to extract and inspect links without clicking them.
- How to use VirusTotal or similar tools to check suspicious URLs.
- How to write a structured triage report with a verdict and recommended action.

> "I built a lab where I analyze phishing emails: I inspect headers, trace links safely, and write triage reports so I can explain how an attack lands and how a SOC would respond."
