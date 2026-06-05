# Changelog

All notable changes to this project are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
versioning is loosely [SemVer](https://semver.org/) at the bundle level.

## [Unreleased]

### Added
- **CI skill-linter** (`scripts/lint_skills.py` + `.github/workflows/skill-lint.yml`) —
  validates every `SKILL.md` for frontmatter, `name` format, description length, and
  body length per `CONTRIBUTING.md`, and scans for leaked secrets and client/engagement
  identifiers. The identifier denylist is stored as SHA-256 hashes
  (`scripts/.identifier-denylist.sha256`) so client names never enter the repo in plaintext.
- **Community infrastructure** — issue templates (bug, new-skill proposal, false-positive),
  PR template, `CODEOWNERS`, `FUNDING.yml`, `CODE_OF_CONDUCT.md`, and this `CHANGELOG.md`.
- **Sponsor** — Atlas Cloud added to the README (theme-adaptive logo).

### Changed
- `.gitignore` now excludes the maintainer-only plaintext denylist override
  (`scripts/.identifier-denylist.local`).

<!-- When PR #7 (20 new hunt-* skills) merges, move its entry up to the next release. -->

## [2.0] - 2026-05-25

### Added
- Report-curation pass: 574 → **681 disclosed-report patterns** across 24 vulnerability classes.
- 5 previously-missing attack surfaces covered; 0 zero-report skills remaining.
- 29 A-to-B chain examples and `ENGAGEMENTS.md` scaffolding.
- Enterprise platform attack matrices (M365/Entra, Okta, SharePoint, vCenter, SSL-VPN, APK, supply-chain).

### Changed
- Top-3 trigger-match concentration rebalanced (81.2% → 68.4%) for better skill routing.

## [1.x]

- Initial public release: 51 skills + 15 slash commands, vendored foundation from
  `shuvonsec/claude-bug-bounty`, Burp MCP integration, recon pipeline.

[Unreleased]: https://github.com/elementalsouls/Claude-BugHunter/compare/v2.0...HEAD
[2.0]: https://github.com/elementalsouls/Claude-BugHunter/releases/tag/v2.0
