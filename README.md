# DVWA Lab — Web Application Penetration Testing Notes

> A structured learning lab for web application penetration testing on [Damn Vulnerable Web Application (DVWA)](https://github.com/digininja/DVWA), documenting exploitation techniques, PHP source code analysis, and remediation notes across multiple vulnerability classes.

![Status](https://img.shields.io/badge/status-in%20progress-blue)
![Platform](https://img.shields.io/badge/platform-DVWA-red)
![Focus](https://img.shields.io/badge/focus-web%20application%20pentesting-purple)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 📖 About

This repository documents my hands-on practice with **web application penetration testing** using DVWA as a controlled, intentionally vulnerable target. Each write-up goes beyond "the payload worked" — it explains **why the vulnerability exists**, analyzes the underlying PHP source code, and records **how to fix it**.

The goal is to build the mindset of a pentester who understands root causes, not just exploitation steps.

**What I'm building here**
- A consistent, repeatable methodology for approaching each vulnerability class
- Source-code–level understanding of common web flaws
- A personal knowledge base for reference during engagements and interviews
- A documented portfolio of hands-on practice

---

## 🛠️ Lab Environment

| Component | Description |
|---|---|
| **Victim server** | Ubuntu Server |
| **Attacker machine** | Kali Linux |
| **Web server** | Apache |
| **Language** | PHP |
| **Database** | MariaDB |
| **Application** | DVWA |
| **Intercepting proxy** | Burp Suite |

The lab is fully isolated — attacker and victim are separate hosts on a private network. No part of this environment is exposed to the internet.

---

## 🎯 Learning Methodology

Every vulnerability is studied through a fixed **6-step process**:

1. **Understand the vulnerability** — definition, root cause, conditions required for exploitation.
2. **Explore the functionality** — identify where and how the feature is exposed in the application.
3. **Exploit** — craft payloads, intercept requests, bypass filters across **Low / Medium / High** security levels.
4. **Analyze the root cause** — read the corresponding PHP source code and explain why the flaw exists.
5. **Remediate** — write the secure version of the code and explain the defensive control.
6. **Verify the fix** — re-test to confirm exploitation is no longer possible.

Each write-up answers **six questions**:

> What is this vulnerability? · What conditions enable it? · How was it exploited? · What was the result? · Why did it work? · How should it be prevented?

---

## 🗂️ Repository Structure

```text
dvwa-lab/
├── docx/                          # Environment setup & foundation docs
│   ├── 01-setup.md                # Installation & configuration
│   ├── 02-network.md              # Network topology between attacker & victim
│   ├── 03-tools.md                # Tools used (Burp, ffuf, sqlmap...)
│   ├── 04-useful-payloads.md      # Reusable payload reference
│   ├── 05-achitecture.md          # DVWA directory structure & key files
│   └── 06-web-tour.md             # Walkthrough of app features
│
├── vulnerabilities/               # One folder per vulnerability class
│   ├── brute-force/
│   ├── command-injection/
│   ├── sql-injection/
│   ├── blind-sql-injection/
│   ├── reflected-xss/
│   ├── stored-xss/
│   └── dom-xss/
│
└── README.md
```

---

## 📚 Vulnerabilities Covered

| # | Vulnerability | OWASP Category | Levels Exploited | Source Code Analysis |
|---|---|---|---|---|
| 1 | Brute Force | A07: Auth Failures | Low / Medium / High | ✅ |
| 2 | Command Injection | A03: Injection | Low / Medium / High | ✅ |
| 3 | SQL Injection | A03: Injection | Low / Medium / High | ✅ |
| 4 | Blind SQL Injection | A03: Injection | Low / Medium / High | ✅ |
| 5 | Reflected XSS | A03: Injection | Low / Medium / High | ✅ |
| 6 | Stored XSS | A03: Injection | Low / Medium / High | ✅ |
| 7 | DOM XSS | A03: Injection | Low / Medium / High | ✅ |

---

## ⭐ Featured Write-ups

A few notes that best represent my methodology:

- **[SQL Injection](./vulnerabilities/sql-injection/)** — from `'` to a working `UNION SELECT` payload, with a step-by-step reasoning trail across error responses and URL encoding.
- **[Blind SQL Injection](./vulnerabilities/blind-sql-injection/)** — boolean-based inference, character-by-character data exfiltration.
- **[Command Injection](./vulnerabilities/command-injection/)** — filter bypass across security levels, with PHP source code analysis.
- **[DOM XSS](./vulnerabilities/dom-xss/)** — client-side sink analysis and how the flaw differs from server-side XSS.

---

## 🧠 Why Source Code Analysis Matters

DVWA offers four security levels — **Low, Medium, High, Impossible** — for every vulnerability. Practicing across them reveals how the same flaw transforms as defenses are added:

| Level | What it teaches |
|---|---|
| **Low** | How the vulnerability forms — no input handling at all |
| **Medium** | How filters are usually written *incorrectly* — blacklists, partial escaping, weak type checks |
| **High** | How to bypass "hardened" code that still has logic gaps |
| **Impossible** | What secure code actually looks like — prepared statements, output encoding, rate limiting |

Reading the **Impossible** level code is where I learned the most: it shows the correct defensive pattern for each vulnerability, which I then apply when remediating my own application.

---

## 🚀 How to Reproduce

The lab is built on a standard LAMP stack. To recreate it:

1. Deploy an **Ubuntu Server** VM and install Apache, PHP, MariaDB.
2. Clone and configure DVWA:
   ```bash
   git clone https://github.com/digininja/DVWA.git
   ```
3. Follow the setup notes in [`docx/01-setup.md`](./docx/01-setup.md).
4. From a **Kali Linux** VM on the same private network, access the DVWA instance and configure Burp Suite as your intercepting proxy.
5. Initialize the database via the DVWA **Setup** page, then set the security level to **Low** to begin.

Full network and configuration details are in [`docx/02-network.md`](./docx/02-network.md).

---

## ⚖️ Disclaimer

DVWA is **intentionally vulnerable** software. This repository is for **educational purposes only** and is intended to be run in an **isolated, private lab environment**. Do not expose DVWA or this lab to the internet. All techniques documented here were performed against systems I own or have explicit permission to test.

---

## 📬 Contact

- **GitHub:** [@ngngocatr](https://github.com/ngngocatr)

---

## 📄 License

Released under the [MIT License](./LICENSE).