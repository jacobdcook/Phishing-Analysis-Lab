# Phishing Analysis Lab

A hands-on lab for analyzing phishing emails using real-world SOC techniques. This project demonstrates how to inspect email headers, trace malicious links safely, and produce structured triage reports.

## Objectives

- Safely obtain and handle phishing email samples (`.eml` format).
- Read and interpret email authentication headers: **From**, **Reply-To**, **Received**, **SPF**, **DKIM**, **DMARC**.
- Extract and inspect embedded links without clicking them.
- Use online scanners (VirusTotal, URLScan.io) to check suspicious URLs.
- Write one-page triage reports with a verdict and recommended response actions.

## Repository Structure

```
Phishing-Analysis-Lab/
├── samples/          # .eml phishing samples
├── screenshots/      # Lab screenshots (evidence)
├── reports/          # Triage reports, templates, and runbook
│   ├── triage-template.md
│   └── runbook-phishing-triage.md
├── .gitignore
├── README.md         # This file
└── TUTORIAL.md       # Full step-by-step tutorial
```

## Getting Started

See [TUTORIAL.md](TUTORIAL.md) for the full step-by-step walkthrough covering:

1. Obtaining phishing samples
2. Mapping email headers
3. Extracting and inspecting links
4. Writing triage reports

---

## Lab Execution & Evidence

The screenshots below document each phase of the phishing analysis workflow. Follow the [Tutorial](TUTORIAL.md) to reproduce these steps and capture your own evidence.

### 1. Email Headers

Open the `.eml` file in a text editor and identify the key authentication headers (From, Reply-To, Received chain, SPF, DKIM, DMARC). Note any mismatches or failures that indicate spoofing.

![Email headers in editor](screenshots/01-eml-headers-in-editor.png)

### 2. Link vs Href

Compare what the user sees (link text) to where the link actually points (`href` attribute). Mismatches are a primary phishing indicator — the visible text may show a legitimate domain while the real URL leads to an attacker-controlled site.

![Link text vs href](screenshots/02-links-vs-href.png)

### 3. VirusTotal Check

Submit extracted URLs to VirusTotal or a similar scanner to check community detection results. Review the detection ratio, flagged categories, and domain registration age.

![VirusTotal result](screenshots/03-virustotal-result.png)

### 4. Triage Report

Document all findings in a structured triage report: suspicious indicators, extracted IOCs, verdict (phishing / not phishing), and recommended action for the SOC.

![Triage report sample](screenshots/04-triage-report-sample.png)

---

## Tools Used

| Tool | Purpose |
|------|--------|
| Text editor (VS Code, Notepad++) | Reading raw `.eml` headers and HTML body |
| `grep` | Extracting `href` values from email source |
| [VirusTotal](https://www.virustotal.com) | URL and file hash reputation checks |
| [URLScan.io](https://urlscan.io) | URL scanning and screenshot capture |
| [PhishTank](https://phishtank.org) | Community-verified phishing URL database |

## Learning Outcomes

This lab builds practical skills that map directly to SOC Analyst (Tier 1) responsibilities:

- **Header analysis** — Understand email authentication and identify spoofed senders.
- **Link inspection** — Spot deceptive URLs without exposing yourself to the payload.
- **Safe URL checking** — Use third-party scanners instead of clicking links.
- **Triage reporting** — Produce clear, actionable reports for escalation or closure.

> "I built a lab where I analyze phishing emails: I inspect headers, trace links safely, and write triage reports so I can explain how an attack lands and how a SOC would respond."
