# Eval harness (v0) — does the skill bundle actually help?

This measures what the rest of the repo asserts but never proved: **do the `hunt-*`
skills let an autonomous agent find/exploit bugs, and by how much vs. the same agent
without them?** It runs a headless agent against a self-grading vulnerable target and
records solve-rate, autonomy, and cost — with a skills-on / skills-off **ablation**.

## How it works
```
challenge ─▶ reset target (clean oracle) ─▶ claude -p (agent: skills + Burp MCP)
          ─▶ poll target's self-graded oracle ─▶ record {solved, turns, cost, tokens}
```
- **Execution engine:** `claude -p` (headless Claude Code). Skills auto-activate from
  `~/.claude/skills/` by description; the Burp MCP (`burp-mcp.json`) gives it HTTP "hands".
- **Ablation:** skills-off runs add `--disable-slash-commands` (disables all skills).
  Everything else identical, model held constant → the delta isolates the skills.
- **Oracle (ground truth):** the target grades itself. v0 uses **OWASP Juice Shop**
  (`GET /api/Challenges` → `solved` booleans). No human scoring.
- **Isolation:** the target is `docker restart`ed before every run, so each run starts
  from a clean all-unsolved state.

## Setup
```bash
# 1. target (self-graded)
docker run -d -p 3001:3000 --name juiceshop bkimminich/juice-shop

# 2. Burp running with the MCP proxy on :9876
cp eval/burp-mcp.json.example eval/burp-mcp.json   # then set your mcp-proxy jar path

# 3. claude CLI authed (it is, if you use Claude Code)
```

## Run
```bash
python3 eval/run_eval.py --limit 1                  # quick proof: 1 challenge, both conditions
python3 eval/run_eval.py                            # full challenge set, skills vs baseline
python3 eval/run_eval.py --conditions skills        # skills-on only
python3 eval/run_eval.py --model claude-opus-4-8    # change model (constant across conditions)
```
Results stream to `eval/results/run.jsonl`; a summary table prints at the end.

## ⚠️ Read this before trusting a number
- **Juice Shop is famous** — its solutions are in model training data. So v0 is a strong
  **pipeline proof + autonomy/cost** number, but a **weak skill-delta** measurement: a
  capable base model already "knows" Juice Shop, so the ablation may show little gap
  (ceiling effect) even where skills genuinely help on novel targets.
- **The real skill-delta needs less-memorized targets** — PortSwigger Web Security Academy
  labs (dynamic, per-account) and novel/obscure apps. Same harness, new oracle adapter.
  That's the next tier.
- **Lab/CTF solving ≠ expert work.** These are single-bug puzzles; they don't measure
  business-logic, chaining, or messy-target judgment — where experts actually spend time.
  A high solve-rate is necessary, not sufficient, evidence.
- **Authorized targets only.** The harness points at a local, deliberately-vulnerable app
  you own. Never point it at targets you aren't authorized to test.

## Files
- `run_eval.py` — orchestrator + ablation runner + metrics
- `challenges.json` — challenge set (key, oracle name, category, skill hint, objective)
- `burp-mcp.json` — Burp MCP config handed to the headless agent
- `results/` — `run.jsonl` (per-run records) + `run.log`

## Roadmap
- v0 (this) — Juice Shop, a few classes, ablation, solve/cost numbers. *Pipeline proof.*
- v1 — PortSwigger lab oracle (real skill-delta), 5–6 classes, per-class breakdown, cost dashboard.
- v2 — multi-class autonomous (agent picks the class), chaining, harden `/autopilot` into this loop.
