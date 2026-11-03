# Progress

Tracks the phases in [ROADMAP.md](ROADMAP.md). Tick items as you finish them, and log metrics for real tasks (ROADMAP §10) so single-agent and team setups can be compared on evidence.

## Current phase

**Phase A: Single-agent mastery**. Started: _not yet_

## Phase status

| Phase | Machine | Status | Started | Exit criterion met |
|---|---|---|---|---|
| A. Single-agent mastery | both | not started | | |
| B. Coding-agent team | both | not started | | |
| C. Multi-model + local | home | not started | | |
| D. Scripted orchestration | home (then work, Copilot only) | not started | | |
| E. Building agents | both | not started | | |
| F. Models | home + cloud GPU | not started | | |

## Courses and resources completed

| Date | Item | Hours | Shipped practice output | Notes |
|---|---|---|---|---|

## Task metrics (ROADMAP §10)

| Date | Task | Setup (single / team / models) | Time to accepted change | Human minutes | Defects found later | Useful vs false review findings | Cost |
|---|---|---|---|---|---|---|---|

## Open questions / decisions

- Work, check yourself first (ROADMAP §3.6), then ask the admin about what's missing:
  - [ ] Copilot CLI installs and signs in
  - [ ] "Copilot" is assignable on issues (coding agent)
  - [ ] Codex shows up as an Agent HQ partner agent
  - [ ] Any non-OpenAI model in the picker (Gemini, Grok, Kimi, MAI)? This decides between cross-vendor and cross-tier review
- VPS (Hermes, ROADMAP §5.1):
  - [x] Note the exact Hetzner plan: CX23, 2 vCPU / 4 GB / 40 GB (2026-11-02)
  - [ ] **Upgrade Hermes** from the local build v0.22.0+119 (2026-09-24) to the latest release (v0.22.6 on 2026-11-02; v0.22.2 minimum, for the 2026 CVE fixes, ROADMAP §5.1)
  - [ ] After the upgrade, check RAM headroom with `free -m` and `docker stats` while a Docker-backend job runs
  - [ ] Check the security checklist in §5.1 against the current Hermes config (allowed users, approvals mode, backend)
  - [ ] Which Claude plan do you have: Pro, or Max/Ultra?
- Home: OpenAI access for codex-plugin-cc
  - [ ] Check platform.openai.com for an existing API key and credit balance (API billing is separate from ChatGPT plans)
  - [ ] Decide between API key (pay per use) and ChatGPT Plus (flat fee, more Codex features) after ~2 weeks of real reviews
