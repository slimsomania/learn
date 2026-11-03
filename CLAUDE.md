# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A personal learning workspace, not a software project. There is no build, lint or test step. The product is a **living, validated learning roadmap** for one learner, covering:

1. **Now:** operating a team of coding agents (planner / implementer / tester / reviewer, cross-model review, worktrees, hook and CI gates).
2. **Next:** building agents and orchestration (MCP, Microsoft Agent Framework, evals, observability, security).
3. **Later:** running models locally and fine-tuning or training them.

## Files

| Path | Role |
|---|---|
| `ROADMAP.md` | The single canonical roadmap: phases A–F, materials, templates, demand evidence. Edit this one; don't start a parallel roadmap. |
| `PROGRESS.md` | The learner's checklist, time log and per-task metrics. Update it when the learner reports progress. |
| `validation-log.md` | An append-only record of every validation pass: date, scope, verdicts, changes made. |
| `notes/` | The learner's study notes and exercise write-ups, one file per phase or course (`notes/phase-b-worktrees.md`). Create it when first needed. |
| `archive/YYYY-MM/` | Superseded research snapshots. Read-only; never edit them. |
| `.claude/agents/` | `resource-researcher` (finds candidates) and `resource-validator` (independently challenges them). |
| `.claude/skills/` | `research-topic` (add or refresh a topic end to end) and `revalidate` (periodic freshness sweep of ROADMAP.md). |

## Learner profile (tailor every recommendation to this)

- A senior developer with 10+ years of commercial experience: React, .NET, Python. Understands LLM basics (tokens, context, tool calling). Leave out beginner material.
- **Work laptop:** GitHub Copilot Business/Enterprise on Azure. **Anthropic models and Claude Code are not allowed for work.** Any work-side workflow must use only Copilot with non-Anthropic models, GitHub Actions, and Azure. Models the learner confirmed: GPT-6.1 Sol/Terra, GPT-6 Sol/Luna and other recent OpenAI models (not GPT-6 Astra). Whether non-OpenAI models are enabled is not yet known. Copilot use today means the IDE and code review; access to the Copilot CLI, coding agent and Agent HQ is not yet confirmed (see ROADMAP §3.6). Never recommend Claude Code, Claude models or the Agent HQ Claude agent for work tasks.
- **Home PC (this machine):** Windows 11, ThinkPad T14 Gen 2, i7-1165G7, 16 GB RAM, Intel Iris Xe, no NVIDIA GPU, WSL available. Tools: Claude Code; a Google AI Pro ("Gemini Pro") subscription, which works through **Antigravity CLI (`agy`)**, not Gemini CLI (Gemini CLI stopped serving AI Pro on 2026-06-18); and Hermes Agent connected to Telegram, used as the front door and notification layer, not the code orchestrator. **Hermes runs on a minimal Hetzner Cloud VPS (smallest tier; exact vCPU/RAM not confirmed), not on the home PC**, so it can't reach the laptop directly, and the laptop may be off. Codex, Ollama and `gh` aren't installed yet, and it's unknown whether the learner has a ChatGPT paid plan. The main home cross-model reviewer is `openai/codex-plugin-cc`.
- Local inference on this machine is limited to small quantized models, and fine-tuning must use cloud GPUs (Colab, Kaggle, rented). Don't assume a dedicated GPU. Recommend hardware purchases only with cost and benefit spelled out.
- Time: works full time and learns daily by using AI tools on real tasks. The roadmap pacing (~8 h/week of deliberate study) is a planning unit, not a cap. Favor learn-by-doing on real repos over long video courses.
- Day-to-day work style today is "reactive" (describe → plan mode → implement → Copilot PR review). The gap is the proactive, multi-agent, orchestrated workflow.

## The core rule: everything recommended must be validated

The learner's hard requirement: anything suggested must still be **valid, widely used and worth learning in the current year**. Tools and techniques that were standard in 2024 may be obsolete now. So:

- **Check the date first.** Always use the current date from the environment. Never trust training-data knowledge for versions, prices, plan tiers, course availability or "experimental vs GA" status. Search.
- **Use two separate roles.** `resource-researcher` proposes; `resource-validator` checks. The validator gets only the claims list and the learner profile, never the researcher's reasoning, so its check stays independent. This mirrors the roadmap's own rule that a reviewer gets the spec and the diff, not the implementer's narrative.
- **Validation criteria** (the validator applies all of them):
  1. *Valid:* the link resolves; the content matches current tool versions and APIs; it isn't deprecated, archived or superseded.
  2. *In use:* evidence of real adoption in the current year (official docs, release cadence, job postings, surveys, GitHub activity).
  3. *Worth learning:* demand or leverage for this learner specifically (their stack, work constraints, hardware).
  4. *Age is not disqualifying by itself.* Older material passes when its ideas are still the applied standard; mark it `evergreen` (for example Karpathy's Zero to Hero, or the patterns in Anthropic's "Building effective agents"). Fast-moving tooling material must match current versions.
  5. *Evergreen is never pre-approved.* Every pass re-reads each evergreen page, not just its link, and looks for banners or notes saying the content is outdated, superseded or moved. If the author has flagged part of it as outdated, the roadmap entry quotes the note and says which part still holds ("concepts only; skip its tooling section").
- **Verdicts:** `Keep` · `Keep (evergreen)` · `Replace → X` · `Downgrade (optional)` · `Drop` · `Unverified`. Don't publish `Unverified` items as recommendations. Either verify them or list them under "watch".
- **Every validation pass** appends an entry to `validation-log.md`. When a roadmap section is revalidated, update its "Last verified" note.
- **Prefer** official docs, changelogs and pricing pages over blogs and aggregators. When only secondary sources exist, say so. Demand numbers differ by source; present them as a direction, not precise figures.

## Roadmap entry conventions (match the existing style in ROADMAP.md)

- Use tables with: linked item · cost · time · what you get / why for this learner. Keep prose short and direct.
- Every recommendation needs a concrete practice output or exit criterion. ("Ship one small thing" is the rule after each course.)
- Pick, don't stack: one framework per stack, at most one certificate. Say explicitly what to skip and why.
- Every phase recommendation names which machine it applies to (work or home), because the tool sets differ.
- Keep the reusable templates in ROADMAP.md §9 stable; the learner copies them into real repos.

## Environment notes

- Windows 11. The Bash tool is Git Bash; PowerShell is also available. Claude Code agent-team split panes need tmux (WSL2). The default in-process mode works natively.
- Git user: Oleksii Kozlovskyi. Default branch `main`. Commit only when asked.
