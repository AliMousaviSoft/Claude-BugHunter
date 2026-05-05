![claude-bughunter banner](assets/banner.svg)

# claude-bughunter

> A self-contained Claude skill bundle for bug hunting · **40 skills** · 15 slash commands · **574+ disclosed-report patterns** across 24 vulnerability classes · engagement-folder scaffolding · Burp MCP integration · battle-tested through real Bugcrowd engagements.

Built by **[ElementalSoul](https://github.com/elementalsouls)** — Bug Bounty & GenAI Security Research.

---

## What is this?

`claude-bughunter` is a drop-in skill bundle for the [Claude Code skills system](https://docs.claude.com/en/docs/claude-code/skills). Install once and Claude Code stops being a chatbot and starts behaving like a senior bug-bounty researcher: it knows the techniques, the chain templates, the VRT mappings, and the hygiene — and it stays in scope.

Three layers stack:

- **`bug-bounty` + `bb-methodology`** — *how to think.* 5-phase non-linear hunting workflow, critical-thinking framework, developer-psychology heuristics, anomaly detection patterns.
- **24 `hunt-*` skills + `security-arsenal`** — *what to look for.* Per-class detection patterns, payloads, bypass tables, and chain templates curated from 574+ disclosed HackerOne reports.
- **`triage-validation` + `bugcrowd-reporting` + `evidence-hygiene`** — *how to ship it.* 7-Question Gate, VRT category fallback, severity-request paragraphs, OOS rebuttals, cookie/PII redaction protocols.

All triggered automatically by topic — describe what you're testing in plain English and the relevant skill loads. No invocation by name.

> **40 skills · 15 commands · 574+ disclosed reports curated · 6-phase workflow · validated through a Bugcrowd financial-target engagement.**

---

## Structure

```
Claude-BugHunter/
├── skills/                                # 40 SKILL.md bundles
│   ├── bug-bounty/SKILL.md                # master orchestrator (vendored)
│   ├── bb-methodology/SKILL.md            # 5-phase workflow + mindset (vendored)
│   ├── bb-local-toolkit/SKILL.md          # full pipeline router
│   ├── osint-methodology/SKILL.md         # 5-stage recon + asset graph
│   ├── offensive-osint/                   # 15-reference probe arsenal
│   │   ├── SKILL.md
│   │   ├── references/                    # progressive-disclosure modules
│   │   └── scripts/secret_scan.py
│   ├── triage-validation/SKILL.md         # 7-Question Gate (vendored)
│   ├── report-writing/SKILL.md            # H1/Bugcrowd/Intigriti templates (vendored)
│   ├── bugcrowd-reporting/SKILL.md        # VRT, OOS rebuttals, severity requests
│   ├── evidence-hygiene/SKILL.md          # cookie/PII/HAR redaction
│   ├── security-arsenal/SKILL.md          # payloads + bypass tables (vendored)
│   ├── web2-recon/SKILL.md                # subdomain enum, host discovery (vendored)
│   ├── web3-audit/SKILL.md                # 10 DeFi bug classes (vendored)
│   ├── meme-coin-audit/SKILL.md           # token rug-pull detection (vendored)
│   └── hunt-*/SKILL.md                    # 24 per-class hunt skills
├── commands/                              # 15 slash commands
├── scripts/
│   ├── hunt.sh                            # engagement-folder scaffolder
│   ├── install.sh                         # single-step installer
│   └── install-community-skills.sh        # optional: refresh upstream
├── docs/                                  # architecture, credits
├── assets/banner.svg
├── INSTALL.md · USAGE.md · CONTRIBUTING.md · SECURITY.md
└── LICENSE
```

Drop the contents of `skills/` into `~/.claude/skills/` and Claude auto-triggers on relevant phrases. The `install.sh` script does this plus copies commands to `~/.claude/commands/` and wires `hunt.sh` into your shell rc.

---

## Skill Index

40 skills across 7 capability domains. Each row maps a capability to the skill that codifies it.

### Web Application Hunting

| Capability | H1 reports | Skill |
|---|---|---|
| Reflected · Stored · DOM · blind XSS · CSP bypass | 174 | `hunt-xss` |
| IDOR / broken object-level authorization · cross-tenant access | 26 | `hunt-idor` |
| SQL injection (classic, blind, time-based) · NoSQL injection | 8 | `hunt-sqli` |
| XML external entity (in-band, OOB, XXE-via-DOCX) | 4 | `hunt-xxe` |
| Cross-site request forgery (chain-required) | 10 | `hunt-csrf` |
| File upload bypass (10 techniques: double-ext, magic-bytes, polyglot, ZIP slip) | curated | `hunt-file-upload` |
| Server-side template injection (Jinja2, Twig, Freemarker, ERB, Spring) | curated | `hunt-ssti` |

### Authentication & Identity

| Capability | H1 reports | Skill |
|---|---|---|
| OAuth 2.0 / OIDC flaws · open-redirect chain · state-parameter abuse | 10 | `hunt-oauth` |
| SAML / SSO attacks · XML signature wrapping · comment injection | curated | `hunt-saml` |
| Account takeover taxonomy — 9 distinct paths + chains | curated | `hunt-ato` |
| MFA / 2FA bypass — 7 patterns (OTP brute, race, recovery dump, factor downgrade) | curated | `hunt-mfa-bypass` |
| Broken authentication / access control | 4 | `hunt-auth-bypass` |

### API & Infrastructure

| Capability | H1 reports | Skill |
|---|---|---|
| SSRF + 11 IP-bypass techniques · cloud metadata exfil | 9 | `hunt-ssrf` |
| RCE — crown-jewel chains, deserialization, code injection | 67 | `hunt-rce` |
| GraphQL — introspection, alias batching, depth, node() IDOR | 3 | `hunt-graphql` |
| API misconfig — mass assignment, JWT attacks, prototype pollution, CORS | curated | `hunt-api-misconfig` |
| Cloud / K8s misconfig — public S3, Lambda URLs, kubelet 10250, Docker 2375 | curated | `hunt-cloud-misconfig` |
| Subdomain takeover — 27+ provider fingerprints + chain to ATO | 11 | `hunt-subdomain` |

### Advanced & Concurrency

| Capability | H1 reports | Skill |
|---|---|---|
| Race conditions / TOCTOU — double-spend, MFA-bypass-via-race | 3 | `hunt-race-condition` |
| Web cache poisoning · cache deception · CDN exploitation | 4 | `hunt-cache-poison` |
| HTTP request smuggling (CL.TE, TE.CL, H2.CL, H2.TE) | curated | `hunt-http-smuggling` |
| Business logic flaws (coupon abuse, balance manipulation) | 7 | `hunt-business-logic` |
| LLM / agentic AI — prompt injection, ASCII smuggling, ASI01-ASI10 | curated | `hunt-llm-ai` |
| Misc — catch-all for less-common classes | 225 | `hunt-misc` |

### Recon & OSINT

| Capability | Skill |
|---|---|
| 15-reference probe arsenal: subdomain enum, identity fabric, secret patterns, sector recon | `offensive-osint` |
| 5-stage recon pipeline · 29-type asset graph · severity rubric · time budgeting | `osint-methodology` |
| Subdomain enumeration · host discovery · URL crawling | `web2-recon` |
| Full pipeline router for local cloned bug-bounty repos | `bb-local-toolkit` |

### Workflow & Validation

| Capability | Skill |
|---|---|
| Master orchestrator · pulls in other skills as needed | `bug-bounty` |
| 5-phase non-linear workflow + critical-thinking framework | `bb-methodology` |
| 7-Question Gate · 4 pre-submission gates · never-submit list | `triage-validation` |
| Payloads, bypass tables, wordlists, gf patterns | `security-arsenal` |

### Reporting & Hygiene

| Capability | Skill |
|---|---|
| H1 / Bugcrowd / Intigriti / Immunefi templates · CVSS 3.1 + 4.0 | `report-writing` |
| **Bugcrowd VRT category fallback · severity-request paragraph · OOS rebuttals · chained-finding patterns** | `bugcrowd-reporting` |
| **Cookie redaction · PII black-bar · HAR sanitization · screenshot hygiene** | `evidence-hygiene` |

### Specialized

| Capability | Skill |
|---|---|
| Smart-contract audit · 10 DeFi bug classes · Foundry PoC template | `web3-audit` |
| Token rug-pull detection · honeypot · LP lock bypass | `meme-coin-audit` |

### Slash Commands (15)

| Command | Purpose |
|---|---|
| `/hunt <target>` | Start hunting on a target — loads scope, picks attack surface |
| `/recon <target>` | Run full recon pipeline — subfinder · dnsx · httpx · katana · nuclei |
| `/scope <asset>` | Check if an asset is in scope before hunting |
| `/triage` | Quick 7-Question Gate (faster than `/validate`) |
| `/validate` | Full 7-Question Gate + 4-gate checklist |
| `/report` | Write submission-ready report — H1/Bugcrowd/Intigriti/Immunefi |
| `/autopilot` | Autonomous hunt loop with configurable checkpoints |
| `/chain` | Build A→B→C exploit chain for higher payouts |
| `/intel <target>` | On-demand CVE / disclosed-report intel |
| `/pickup <target>` | Resume previous hunt — shows history + suggestions |
| `/surface <target>` | Ranked attack surface from recon + memory |
| `/remember` | Log finding or pattern to hunt memory |
| `/memory-gc` | Inspect / rotate hunt-memory JSONL files |
| `/token-scan` | Meme-coin / token security scan |
| `/web3-audit <contract>` | Smart-contract 10-class checklist |

---

## Architecture

40 skills across 6 phases, with a 24-skill `hunt-*` sub-stack, an integration layer (Burp MCP, the `hunt` shell command, optional Anthropic + HackerOne APIs), and a usage decision tree for picking the right skill per task.

![architecture overview](assets/architecture-overview.svg)

For deeper reference views — a 3-layer stack architecture and an engagement pipeline with the 4 branched outcomes from the Validate gate — see [`docs/architecture.md`](docs/architecture.md).

---

## The 7-Question Gate

Before drafting any report — `/triage` or `/validate` runs every candidate finding through:

1. Can an attacker use this RIGHT NOW with a real HTTP request?
2. Is the impact on the program's accepted-impact list?
3. Is the asset in scope?
4. Does it work without privileged access an attacker can't get?
5. Is this not already known or documented behavior?
6. Can impact be proved beyond "technically possible"?
7. Is this not on the never-submit list?

One NO = KILL. Move on. This single discipline separates productive researchers from N/A noise.

---

## Quick Start

### One-step install (recommended)

```bash
git clone https://github.com/elementalsouls/Claude-BugHunter.git
cd Claude-BugHunter
./scripts/install.sh
```

That copies all 40 skills to `~/.claude/skills/`, all 15 commands to `~/.claude/commands/`, and sources `hunt.sh` from your shell rc.

### First engagement

```bash
hunt acme-bb              # scaffolds ~/Targets/acme-bb/ with CLAUDE.md, scope.md, findings/, evidence/, submissions.txt
cd ~/Targets/acme-bb
claude                    # opens Claude Code with engagement context
```

In Claude, describe what you're testing in plain English — *"I see a `?url=` parameter, looks SSRF-prone"* — and `hunt-ssrf` auto-loads with detection patterns, payloads, and the 11-bypass IP table.

### Drop-in usage (Claude.ai, Claude API, other harnesses)

```bash
# Copy a single SKILL.md into a Project's system prompt:
cat skills/hunt-rce/SKILL.md | pbcopy
# Paste into Claude.ai project instructions or API system prompt.
```

All skills are plain Markdown — also usable as personal cheat sheets without Claude.

For Burp Suite Pro MCP integration and the optional skill-regenerator setup, see [INSTALL.md](INSTALL.md). For the full workflow walkthrough with a worked example, see [USAGE.md](USAGE.md).

---

## Authorization

These skills are intended for assets you **own** or have **written authorization to assess** (bug-bounty in-scope assets, pentest engagement letters, CTF challenges, your own infrastructure).

The skills include validation gates that auto-trigger when you point Claude at unverified third-party targets — `triage-validation`'s 7-Question Gate explicitly asks whether the asset is in scope (Q3) and on the program's accepted-impact list (Q2). The `bugcrowd-reporting` skill includes researcher-side hygiene (Bugcrowdninja alias, account-state restoration, friendly-tester posture) that signals legitimate authorized testing to the target's fraud team.

The bundle explicitly **excludes**: weaponizing 0-days against unauthorized targets, post-exploitation tooling, malware development, mass-targeting infrastructure. See [`SECURITY.md`](SECURITY.md) for the full posture.

---

## Documentation

| Doc | Contents |
|---|---|
| [`README.md`](README.md) | This file — capability map, structure, quick start |
| [`INSTALL.md`](INSTALL.md) | Full setup with Burp MCP integration and optional skill regenerator |
| [`USAGE.md`](USAGE.md) | Workflow walkthrough · decision tree · worked engagement example |
| [`docs/architecture.md`](docs/architecture.md) | 6-phase architecture · skill-to-phase mapping · engagement composition |
| [`docs/credits.md`](docs/credits.md) | Full attribution: 32 original skills + 8 vendored from upstream |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | PR guidelines · skill quality standards · scope |
| [`SECURITY.md`](SECURITY.md) | Authorized-use posture · responsible disclosure · what's excluded |
| [`LICENSE`](LICENSE) | MIT |

---

## Why this exists

Most bug-hunting Claude setups are either too generic (one big "security" prompt) or too fragmented (you bookmark 30 disclosed reports and re-read them every engagement). Neither scales past the second target.

This bundle was built and validated through a real Bugcrowd engagement on a major financial target. That engagement exposed four capability gaps that a starter 3-skill stack could not close:

1. **No hypothesis discipline** — drafts written before validation → wasted hours, hurt validity ratio
2. **No per-program reporting tactics** — VRT defaults auto-downgraded P3-worthy findings to P4
3. **No engagement coordination** — findings, evidence, and submission IDs scattered across folders
4. **No evidence hygiene** — screenshots leaked cookies and victim PII

The 24 per-class `hunt-*` skills address gap-zero (*"what should I look for"*) by codifying patterns from 574+ disclosed HackerOne reports — Claude knows the actual chain templates real triagers paid for, not abstract OWASP Top 10. The other gaps are addressed directly by `triage-validation` (vendored), `bugcrowd-reporting`, `evidence-hygiene`, and the `hunt` shell scaffold (originals).

---

## Roadmap

- [ ] HackerOne MCP integration (currently only Burp MCP wired in)
- [ ] Per-engagement memory layer — pattern recall across targets
- [ ] Industry-specific hunt skills — `hunt-fintech-graphql`, `hunt-healthcare-fhir`, `hunt-gov-compliance`
- [ ] Program-rules-parser skill — auto-generate structured `scope.md` from program text
- [ ] Refresh `hunt-*` skills with newer disclosed reports (re-run `public-skills-builder`)
- [ ] CHANGELOG.md and CODE_OF_CONDUCT.md (matching Claude-OSINT layout)

---

## About

Operational tradecraft accumulated across bug-bounty engagements and authorized pentests, codified into Claude skills. Platform-agnostic — slot into any engagement workflow you already use, or none.

**Author:** [ElementalSoul](https://github.com/elementalsouls) · GenAI Security Research

**Sister project:** [Claude-OSINT](https://github.com/elementalsouls/Claude-OSINT) — paired skills for the recon phase that this bundle picks up after.

**Vendored foundation:** [shuvonsec/claude-bug-bounty](https://github.com/shuvonsec/claude-bug-bounty) — methodology, validation, reporting, payload library (8 of 40 skills + 15 slash commands)

**Generator tool used (not vendored):** [shuvonsec/public-skills-builder](https://github.com/shuvonsec/public-skills-builder) — used to scaffold per-class skills from H1 disclosed reports

**Inspirations:**
- [archangel / douglasday](https://hackerone.com/) — top-10 H1 hunter; per-class skill pattern
- [Trail of Bits — `trailofbits/skills`](https://github.com/trailofbits/skills) — skill-authoring discipline
- [SecSkills — `trilwu/secskills`](https://github.com/trilwu/secskills) — subagent pattern

**Tool inventory:**
- [PortSwigger Burp Suite + MCP Server extension](https://portswigger.net/burp)
- [ProjectDiscovery](https://github.com/projectdiscovery) — subfinder · dnsx · httpx · katana · nuclei
- [SecLists](https://github.com/danielmiessler/SecLists) · [Assetnote Wordlists](https://wordlists.assetnote.io/)

**License:** [MIT](LICENSE) — use freely, attribution appreciated.

---

> *"Give Claude the right skill and it stops being a chatbot. It becomes an operator."*
