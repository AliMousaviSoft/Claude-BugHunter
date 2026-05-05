# Security Policy

## Scope and authorized-use posture

`claude-bughunter` is a knowledge bundle. It contains methodology, payloads, bypass tables, detection patterns, and reporting templates derived from publicly disclosed bug-bounty reports and authorized engagements.

The skills are intended for use against assets you **own** or have **written authorization to assess**:

- Bug-bounty programs where the asset is explicitly in-scope (HackerOne, Bugcrowd, Intigriti, Immunefi, YesWeHack, etc.)
- Authorized penetration-testing engagements with a signed RoE
- Capture-the-flag (CTF) competitions
- Your own infrastructure
- Security research on synthetic / lab targets

The skills include validation gates that auto-trigger when ambiguity arises:

- `triage-validation`'s 7-Question Gate — Q3 explicitly asks whether the asset is in scope; Q2 asks whether the impact is on the program's accepted-impact list
- `bugcrowd-reporting`'s researcher-side hygiene — Bugcrowdninja email alias, account-state restoration, friendly-tester posture (signals authorized testing to fraud teams)
- `evidence-hygiene`'s redaction protocols — protect both your account secrets and other-user PII even when impact crosses tenants

## What this bundle explicitly excludes

The bundle does **not** include and is **not intended for**:

- Weaponizing 0-day exploits against unauthorized targets
- Post-exploitation tooling, persistence mechanisms, or lateral-movement techniques
- Malware development, command-and-control frameworks, or stealth-evasion guidance
- Mass-targeting infrastructure or unauthorized scanning at scale
- Supply-chain compromise of unaffiliated upstream projects
- Credential stuffing, account-takeover automation, or fraud against systems you don't have authorization to test
- Any activity that violates the Computer Fraud and Abuse Act (CFAA), the UK Computer Misuse Act, India's IT Act, the EU Cybercrime Directive, or equivalent local law in your jurisdiction

If a finding requires going beyond authorized scope to demonstrate impact, the bundle's validation gates default to **DOWNGRADE** or **CHAIN REQUIRED** — never to "exploit further to prove it."

## Reporting a security issue in this repo

If you discover a security issue in **this repository itself** (not in a target you're hunting):

- **Skill content** that you believe could enable abuse against unauthorized targets without commensurate defensive value: open a GitHub issue with the `security` label, or contact the author at the address listed on the [GitHub profile](https://github.com/elementalsouls).
- **Vulnerabilities in installer scripts** (`scripts/install.sh`, `scripts/install-community-skills.sh`, `scripts/hunt.sh`): same channels.
- **Sensitive content accidentally shipped** (engagement-specific data, real account UIDs, real bounty amounts): flag immediately — these will be sanitized in a follow-up commit.

Please **do not** post issues that include unauthorized exploitation evidence against third-party targets.

## Disclosure of vulnerabilities found *using* this bundle

When the bundle helps you find a vulnerability in a target you're authorized to test:

1. **Validate first** — run `/triage` or `/validate` (7-Question Gate)
2. **Capture evidence with hygiene** — `evidence-hygiene` for cookie redaction, PII black-bar, HAR sanitization
3. **Submit responsibly** — through the program's official channel (HackerOne / Bugcrowd / Intigriti / Immunefi report form, or the program's `security@` / `vdp@` mailbox)
4. **Coordinate disclosure** — respect the program's confidentiality terms; don't tweet/blog the finding until the program publicly discloses
5. **Rotate test-account credentials** — `evidence-hygiene` §8 covers post-submission hygiene

The bundle's `report-writing` and `bugcrowd-reporting` skills produce platform-ready submission templates with CVSS 3.1 scoring, impact statements, and reproduction steps — use them.

## Responsible-use commitments by users of this bundle

By using `claude-bughunter`, you acknowledge:

- You are responsible for ensuring you have authorization to test any target you point Claude at
- You will respect program scope, RoE, and the spirit (not just the letter) of bug-bounty rules
- You will not use the bundle to harm users of the targets you're testing (no real-PII exfiltration beyond what's necessary to demonstrate impact, no service degradation, no DoS)
- You will follow the redaction protocols in `evidence-hygiene` when capturing and submitting evidence
- You will rotate any credentials, tokens, or session cookies that appear in PoC artifacts after submission

## License and liability

This software is provided "as is" under the [MIT License](LICENSE), without warranty of any kind. The author is not liable for misuse, unauthorized testing, legal consequences of how the bundle is used, or any damages arising from its use.

If you're unsure whether a target is in-scope, or whether a planned action is authorized: **stop and verify in writing** before proceeding.
