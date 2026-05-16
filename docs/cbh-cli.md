# `cbh` — claude-bughunter CLI

> Native runner that bridges the skill content into a real engagement loop.
> Four subcommands compose intake → reconnaissance → triage → submission.
> Stdlib + optional `subfinder` for richer recon. No build step.

## Install

```bash
# Symlink the script to PATH (Linux / macOS)
chmod +x scripts/cbh.py
ln -sf "$(pwd)/scripts/cbh.py" /usr/local/bin/cbh

# Verify
cbh --help
```

Or run inline from a repo checkout:

```bash
scripts/cbh.py --help
```

## The four subcommands

### `cbh recon <target>` — passive recon + live-host probe

```bash
cbh recon hackerone.com
```

Pipeline (in order):

1. **Passive subdomain enumeration** — `crt.sh` certificate transparency (always, no key needed) + `subfinder` (if installed) merged + deduplicated.
2. **DNS resolution** — stdlib `socket.getaddrinfo()` to dodge the `dnsx` segfault issue documented on macOS arm64 (per `web2-recon` Operator Notes).
3. **HTTP probe** — concurrent (10 threads) `urllib.request` → status code, Server header, X-Powered-By, X-Drupal-Cache, and `<title>` extraction.
4. **Summary** — writes `recon/<target>/RECON_SUMMARY.md` with the live-host table and a "Suggested next moves" pointer to `classify`.

Outputs:
- `recon/<target>/subdomains.txt`
- `recon/<target>/resolved.txt`
- `recon/<target>/live-hosts.json`
- `recon/<target>/RECON_SUMMARY.md`

### `cbh classify <url>` — pattern-match URL → hunt-* skills

```bash
cbh classify "https://api.target.com/v1/users/42?next=https://evil.com"
```

Two-stage matcher:

1. **URL-pattern triggers** (high confidence) — 18 hand-curated regexes mapping URL shapes to `hunt-*` skill names. Examples:
   - `[?&](url|next|redirect|return)=` → `hunt-ssrf`
   - `/api/users/{id}` → `hunt-idor` + `hunt-api-misconfig`
   - `/graphql` → `hunt-graphql`
   - `/oauth/(authorize|token|callback)` → `hunt-oauth`
   - `/_layouts/15/` or `/_vti_bin/` → `hunt-sharepoint`
   - `/functionRouter` → `hunt-rce` + `hunt-ssti` (Spring Cloud Function CVE-2022-22963)
   - `/cli` or `/jnlpJars` → `hunt-rce` (Jenkins CVE-2024-23897)
2. **Description-keyword match** (lower confidence) — keyword overlap against each skill's `description:` frontmatter.

Output includes a pointer to the matched skill's Pattern Library doc in `docs/disclosed-reports/<skill>.md` when one exists.

### `cbh triage <finding.md>` — 7-Question Gate

```bash
cbh triage findings/idor-2026-05-15.md
```

Runs all 7 questions from the `triage-validation` skill against the finding text. Returns:

- **PASS** — all 7 answered with evidence. Eligible for `cbh report`.
- **DOWNGRADE** — failed Q2 (severity) or Q5 (duplication) only. Continue with tempered severity claim.
- **KILL** — failed multiple questions OR Q7 matched the never-submit list (self-XSS, missing security headers, etc.). Per `triage-validation` discipline: do not draft the report.

The gate matches keyword signals per question; absence-of-evidence is treated as "not answered". This catches the most common Phase 2D-verified FP shapes:

- Q1 missing curl/POST/GET → finding wasn't actually tested
- Q6 missing "leaked / exfiltrated / oob callback" → impact is "technically possible" only
- Q7 hit on "self-xss / rate-limit only / clickjacking" → automatic KILL

### `cbh report <finding.md> [--platform h1|bugcrowd|intigriti|immunefi] [--out path]`

```bash
cbh report findings/idor-2026-05-15.md --platform bugcrowd --out submissions/h1-draft.md
```

Parses the finding's YAML frontmatter + section headings, emits a platform-specific draft:

- **H1** — common template; CVSS optional.
- **Bugcrowd** — adds VRT mapping + severity-request paragraph (per `bugcrowd-reporting` skill).
- **Intigriti** — common template + CVSS 3.1 vector slot.
- **Immunefi** — Foundry-PoC-required structure; `forge test --match-test` invocation pre-filled.

The draft will have `(fill in)` placeholders wherever the finding text didn't include the relevant section. **The CLI never invents content** — the operator owns each placeholder.

## Composition example — full engagement loop

```bash
# Day 1 — intake
cbh recon target.com
# → recon/target.com/RECON_SUMMARY.md

# Day 2 — hunt
# Find an interesting URL in the recon summary, classify it
cbh classify "https://api.target.com/v1/users/42?token=abc"
# → matches hunt-idor + hunt-api-misconfig + hunt-ssrf
# → read docs/disclosed-reports/hunt-idor.md for the IDOR pattern library

# Day 3 — validate
# Wrote the finding up as a markdown
cbh triage findings/idor.md
# → PASS — eligible for report drafting

# Day 4 — submit
cbh report findings/idor.md --platform h1 --out drafts/h1-draft.md
# Review the draft, fill in placeholders, attach evidence, submit
```

## What the CLI does NOT do

- **Does not auto-attack.** It surfaces candidates and applies discipline rules. The operator runs the actual probes.
- **Does not invent finding content.** Sections without source text become `(fill in)` placeholders.
- **Does not bypass platform rules.** The Bugcrowd VRT mapping is left to the operator — the CLI emits the structural slot, not the choice.
- **Does not replace the skill content.** The CLI is a router into the skill content; the skills are still where the operator-grade depth lives.

## Why this exists

Every other bug-bounty toolchain is either (a) a payload list with no methodology, or (b) a methodology PDF with no runner. This CLI bridges the two: it consumes the repo's skill content and produces engagement-stage outputs. The `recon → classify → triage → report` flow mirrors the 6-phase workflow that `bb-methodology` describes, with the discipline rules from `triage-validation` enforced programmatically at the triage gate.

For senior pentesters: a productivity multiplier that does the boring orchestration so you stay in the interesting parts.

For junior researchers: a guardrail that prevents the top three N/A-submission classes (no real HTTP test, no concrete impact, finding on never-submit list).
