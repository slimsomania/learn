# Generalized Approach: AI Agent Engineering Roadmap (Nov 2026)

Prepared: November 2, 2026
For: a senior developer (React, .NET, Python) who uses AI tools daily and wants to (1) run a team of coding agents, (2) build agents and orchestration, (3) run and adapt models locally.
Merges: [`archive/2026-11/Claude - Agentic AI Learning Path 2026.md`](archive/2026-11/) and [`archive/2026-11/Copilot - AI-agent-workflow-learning-roadmap.md`](archive/2026-11/), with corrections from a fresh source check.
Status: canonical, living document. Progress is tracked in [PROGRESS.md](PROGRESS.md); every validation pass is logged in [validation-log.md](validation-log.md).

---

## 0. Your questions → where this file answers them

| # | Your question | Short answer | Section |
|---|---|---|---|
| Q1 | How do developer / tester / reviewer agents form one team? | A "role" is a config file (instructions + allowed tools + model), not a separate trained model. A team = several roles + an orchestrator + gates. | 2, 3 |
| Q2 | How do agents communicate? | Through artifacts (spec, plan, diff/commit, test output, structured result), or through a shared task list / messages if the harness supports it. Never "telepathically". | 3.2 |
| Q3 | How do they hand work to each other? | A handoff = exact commit + acceptance criteria + checks run + open issues, in a fixed format. | 3.3, 9 |
| Q4 | How do I orchestrate it properly? | Level 1: you, by hand. Level 2: the harness (subagents, agent teams). Level 3: your own code (headless CLI / Agent SDK / Agent Framework). Gates are always code. | 3.4, 5 |
| Q5 | How do I use different models together? | Model routing per role, cross-model review, and one API layer that can switch providers. | 4 |
| Q6 | How do I run models locally? | Ollama / LM Studio / llama.cpp; Claude Code and Codex can point at a local server. | 7 |
| Q7 | How do I build / train models? | Start with fine-tuning (LoRA via Unsloth/TRL); build-from-scratch (Karpathy, CS336) is a separate, long specialization. | 8 |
| Q8 | Is LangGraph still relevant in 2026? | Yes, in Python agent apps, but not needed for running coding agents. For .NET, Microsoft Agent Framework comes first. | 6.7 |
| Q9 | What does the market actually ask for? | Agents, LLM integration, RAG, MCP, evals and observability, Python, Azure. Fine-tuning and certificates come much lower. | 11 |

---

## 1. Plan at a glance

At about 8 hours/week:

| Phase | Machine | Weeks | Goal | Exit criterion |
|---|---|---|---|---|
| 0. Prerequisites | VPS + work + home | A few days, before week 1 | Close known security gaps; start the requests that take time | Hermes on the latest release with the §5.1 security checklist passed; §3.6 admin questions sent; OpenAI access for codex-plugin-cc decided ([PROGRESS.md](PROGRESS.md)) |
| A. Single-agent mastery | both | 1–2 | One harness configured properly on your repo | A feature shipped via plan → implement → verify with repo instruction files and two skills; §10 metrics recorded for ≥3 single-agent tasks (your baseline) |
| B. Coding-agent team | both | 3–6 | Weeks 3–4: implementer + reviewer, orchestrated by you (§3.4 level 1). Weeks 5–6: add tester and planner as subagents (level 2), kept only if the metrics show they help. Worktrees, cross-model review, hook/CI gates, §6.5 security rules | One React + .NET feature through the full loop; reviewer read-only via permissions; metrics compared with the Phase A baseline |
| C. Multi-model + local | home | 7–8 | Route roles to different models by evidence; one local model as a background batch job (§7) | Same task run on 2–3 model setups with cost/quality compared |
| D. Scripted orchestration | home; work variant in §5 | 9–12 | A script runs implement → test → review without you; Hermes can start it from Telegram | Timeouts, retry limits and approval stop work; failures are visible |
| E. Building agents | both | Months 4–6 | Agent apps in .NET (and Python if needed), MCP servers, evals, observability, security, RAG | MCP server + agent + 30-case eval suite running in CI |
| F. Models | home + cloud GPU | Month 7+ | Fine-tune a small open model first; theory (transformers from scratch) for depth after | A LoRA adapter that beats the prompt-only baseline on your eval set |

Rule: if a phase's exit criterion isn't met, repeat the phase. Don't add more agents to fix it.

---

## 2. Core concepts (learn these words precisely)

| Term | Meaning | Practical consequence |
|---|---|---|
| Model | The LLM that produces text and tool calls | Picks quality, cost, latency, context size |
| Harness | The program around the model: file edits, shell, permissions, sessions (Claude Code, Codex, Copilot) | The same model behaves differently in different harnesses |
| Role / custom agent / subagent | A Markdown file with instructions, allowed tools and an optional model | Your "tester" is a config, not a new model |
| Subagent | A worker with its own fresh context; returns a summary to the parent | Keeps the main context clean; parent = orchestrator |
| Agent team | Several full sessions with a shared task list and messaging (Claude Code, experimental) | More parallelism, more coordination cost |
| Skill | A reusable procedure (SKILL.md + scripts) loaded on demand | "Add EF Core migration" written once, reused by every role |
| Hook | Shell command run on lifecycle events; can block an action | The way to enforce rules; prompts are only suggestions |
| MCP | Standard protocol to expose tools/data to any harness | Build one server, use it in Claude Code, Codex, Copilot |
| A2A | Standard protocol for agents (possibly from different vendors) to call each other | Relevant when agents live in different services/orgs |
| Worktree | Extra working directory on its own branch, same repo | Isolates files; does NOT isolate ports, DBs, credentials |
| Eval | A repeatable test of model/agent behavior | The only way to know if a change helped |

Read first (2–3 h total, all free):
- [Building effective agents, Anthropic](https://www.anthropic.com/engineering/building-effective-agents) (Dec 2024; evergreen *concepts only*): workflows vs agents; the five base patterns (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer). Anthropic's own banner says "much of the tooling landscape described in this post has changed since December 2024", so skip its framework list and tooling advice. The patterns map onto the orchestrations in Agent Framework and the OpenAI Agents SDK (§3.2).
- [A practical guide to building AI agents, OpenAI (web version)](https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/): single-agent vs manager vs decentralized handoff designs, guardrails (2025, evergreen concepts; the old PDF's model names such as gpt-4o and o1 were dated, so check whether the web version updated them).
- [Effective context engineering for AI agents, Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) (Sep 2025; evergreen): what each worker should and shouldn't see.

Exit criterion: [notes/section-2-core-concepts.md](notes/section-2-core-concepts.md). It defines each term in your own words, maps the five patterns onto your current workflow, and lists what a reviewer should and shouldn't see. It also holds the **prompt-structure checklist** (background · instructions · tool guidance · output description) from the context-engineering article; use it for every role file and system prompt.

Last verified: Nov 2, 2026.

---

## 3. How a team of coding agents works (Q1–Q4)

### 3.1 Roles

| Role | Input | Output | Tools allowed | Suggested model |
|---|---|---|---|---|
| Planner | Requirement, repo | Spec + task list + API contract | Read-only | Strongest reasoning model |
| Implementer (FE / BE) | One task + contract | Commit on own branch/worktree + handoff record | Edit, shell, tests | Strong coding model |
| Tester | Spec + contract (not the implementer's narrative) | New tests, coverage gaps, results | Edit tests only | Can be cheaper |
| Reviewer | Spec + exact diff | Findings with file/line + evidence | Read-only (enforced) | **Different model from the implementer** |
| Orchestrator | Task list, results | Next step, integration, stop/escalate | Git, CI, launching workers | You first; then a script |

Start with **implementer + reviewer only**. Add the tester and planner when you can measure that they help.

### 3.2 How they communicate

There are only three mechanisms:

1. **Parent ↔ child (subagents).** The parent sends a task prompt; the child works in fresh context and returns a result to the caller. In Claude Code, named subagents can also be messaged or resumed (`SendMessage`), and a subagent can spawn nested subagents up to 3 levels deep by default (`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH=1` disables it). `isolation: worktree` branches from the default branch, not the parent's HEAD. Codex enables subagents by default; custom agents are TOML files in `.codex/agents/`.
   - [Claude Code subagents](https://code.claude.com/docs/en/sub-agents) · [Codex subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) · [Copilot custom agents](https://docs.github.com/en/copilot/concepts/customizations/custom-agents) (public preview in JetBrains, Eclipse, Xcode)
2. **Shared task list + messages (agent teams).** A lead creates tasks; teammates claim them, message each other, and hooks (`TaskCreated`, `TaskCompleted`, `TeammateIdle`; exit code 2 blocks) can reject work. Still experimental as of Nov 2026: enable it with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Teams need an interactive session (none under `-p` or the Agent SDK). Split panes don't work in Windows Terminal or the VS Code terminal, so use the default in-process mode on Windows. Build on subagents plus hooks first (`model:`, `isolation: worktree`), and add teams second. (Last verified: Nov 2, 2026.)
   - [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams)
3. **Artifacts in the repo.** Spec files, plan files, progress notes, commits, PRs, CI results. This works across any tools and models, and it's the most reliable mechanism.
   - [Effective harnesses for long-running agents, Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents): progress file + git history as memory between sessions.
   - [GitHub Spec Kit](https://github.com/github/spec-kit) (v1.2.x, 145k+ stars): `/speckit-constitution` → `/speckit-specify` → `/speckit-plan` → `/speckit-tasks` → `/speckit-implement`, each producing a Markdown artifact that any agent consumes (38 integrations incl. Copilot, Codex, Claude; use `generic` for `agy`). This is "spec-driven development", a widely adopted 2026 handoff format (its "standard" status rests on blog commentary, not surveys).

In code frameworks the same ideas exist as named patterns: **sequential, concurrent, handoff, group chat, manager (Magentic)**.
- [Microsoft Agent Framework orchestrations](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations/) (all five stable since MAF 1.0, Apr 2026)
- Optional, concept read: [OpenAI Agents SDK handoffs](https://openai.github.io/openai-agents-python/handoffs/) (`openai-agents` 0.24, still pre-1.0)
- [How Anthropic built its multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system) (Jun 2025; evergreen): real lessons on lead/worker delegation, token cost (~4× chat for one agent, ~15× for multi-agent, in Anthropic's 2025 system), and when multi-agent fails. Coding has fewer parallelizable subtasks than research.

### 3.3 How they hand off work

A handoff is valid only if it names an **exact commit** and says what was and wasn't verified. Use the templates in section 9 unchanged. Rules:
- The reviewer gets requirements + diff, **not** the implementer's explanation (it biases the review).
- If the candidate commit changes, earlier review/test results are void.
- Each implementer owns specific files; two writers on one file = conflicts.
- Contract first: agree on request/response types and error cases before parallel FE/BE work.

### 3.4 How to orchestrate (three levels)

| Level | Who orchestrates | Use when | Learn from |
|---|---|---|---|
| 1. Manual | You run sessions and pass artifacts | Phase B weeks 3–4; always first | Sections 3.3 and 9 |
| 2. Harness | Main Claude Code session + subagents / agent team; Copilot cloud agent per issue | Phase B weeks 5–6 onward: repeatable roles in one tool | [Subagents](https://code.claude.com/docs/en/sub-agents), [Agent teams](https://code.claude.com/docs/en/agent-teams), [Copilot cloud agent best practices](https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results) |
| 3. Code | Your script/service launches workers | Phase D: the manual flow already works repeatedly | [Claude Code headless](https://code.claude.com/docs/en/headless), [Codex non-interactive](https://learn.chatgpt.com/docs/non-interactive-mode), [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview) |

**Gates are code, not prompts:** tests, type checks, linters, [Claude Code hooks](https://code.claude.com/docs/en/hooks) (exit code 2 blocks the action on events that support blocking, such as `PreToolUse`, and feeds stderr back to the agent; `PostToolUse` can't block, and exit 1 doesn't block), GitHub branch protection + required checks.

### 3.5 Materials for phases A–B

Each row is tagged with its machine and phase. Phase A rows come first.

| Item | Cost · time | Why |
|---|---|---|
| (home, A) [Claude Code in Action](https://academy.claude.com/courses/claude-code-in-action) (1 h) + [Introduction to subagents](https://academy.claude.com/courses/introduction-to-subagents) (45 min) + [Introduction to agent skills](https://academy.claude.com/courses/introduction-to-agent-skills) (1 h) | Free · ~3 h + practice | CLAUDE.md, permissions, hooks, headless, plugins; subagents and skills come from the two short courses |
| (both, A) [Claude Code skills](https://code.claude.com/docs/en/skills) · [AGENTS.md](https://agents.md) | Free · 1 h | Shared instruction format; AGENTS.md (Agentic AI Foundation, Linux Foundation) is read by Codex, Copilot and others |
| (work, A) [Copilot cloud agent module](https://learn.microsoft.com/en-us/training/modules/github-copilot-code-agent/) (~40 min) + [Copilot instructions & custom agents](https://learn.microsoft.com/en-us/training/modules/configure-customize-github-copilot-visual-studio-code/) (~1.7 h); optional skim: [Copilot Fundamentals Part 1](https://learn.microsoft.com/en-us/training/paths/copilot/) (only the Across Environments, Management/customization and Spaces modules) | Free · ~2.5 h (~7.6 h with the path) | Your work stack: `.github/copilot-instructions.md`, `.github/agents`, skills, issue → PR |
| (home, B) [Claude Code: A Highly Agentic Coding Assistant (DeepLearning.AI)](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant) | Free during DLAI beta (certificate needs Pro) · 2 h; updated Aug 2026 | Worktrees, parallel sessions, GitHub integration, hands-on |
| (home, B) [Claude Code worktrees](https://code.claude.com/docs/en/worktrees) + [git worktree](https://git-scm.com/docs/git-worktree) (evergreen) | Free · 1 h | Isolation for parallel writers |
| (home, B) [Codex docs: subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents), [Windows sandbox](https://learn.chatgpt.com/docs/windows/windows-sandbox) | Free · 2 h | Codex as your second harness / reviewer. Runs natively in PowerShell; WSL optional |
| (both, B) §6.5 security: the OWASP Agentic Top 10 and the practical rules | Free · ~2 h | Required before agents get shell, git or network access. It sits in §6 because Phase E builds on it |

Windows note: everything works natively except the agent-teams split-pane view, which needs tmux and doesn't work in Windows Terminal or the VS Code terminal (use the default in-process mode).

**Shared instruction format for both machines:** use `AGENTS.md`. Copilot, Codex and Hermes read it. Claude Code reads it natively since v2.1.277, but only when there's no `CLAUDE.md`; set Project instructions to `claude-md-and-agents-md` to load both. Skill folders differ: Copilot reads `.github/skills`, `.claude/skills` or `.agents/skills`; Codex reads `.agents/skills`; Claude Code reads only `.claude/skills`. Copilot's cloud agent also reads a single root-level `CLAUDE.md` or `GEMINI.md`, so keep Anthropic-specific instructions out of work repos.

### 3.6 Work-side team: Copilot only, no Anthropic models (validated Nov 2, 2026)

Everything in 3.1–3.4 maps onto Copilot Business/Enterprise:

| Concept | Copilot equivalent |
|---|---|
| Role file | [Custom agent](https://docs.github.com/en/copilot/reference/custom-agents-configuration) `.github/agents/<role>.agent.md`: `description` (required), `name`, `target`, `tools`, **`model`**, `mcp-servers`, `handoffs`, `user-invocable`, `disable-model-invocation`. `handoffs` and `argument-hint` are ignored on github.com; `mcp-servers` is ignored in IDEs; `infer` is retired. Works in VS Code, Copilot CLI and the github.com cloud agent (JetBrains/Eclipse/Xcode in preview); repo-, org- or enterprise-level |
| Parallel subagents | Copilot CLI `/fleet` (announced Apr 1, 2026): parallel subagents with their own contexts that can use your `.github/agents` roles; `/tasks` shows progress. Each subagent consumes its own AI credits |
| Cloud implementer | Copilot cloud agent (issue → PR), and **Codex as a third-party coding agent (Agent HQ)** as a second cloud agent (Business since Feb 26, 2026; still public preview; billed in GitHub AI Credits plus Actions minutes; models Auto, GPT-5.3-Codex, GPT-5.4 nano). Business needs two admin switches: Enterprise AI Controls → Agents → Partner Agents, and Org → Copilot → Coding agent → Partner Agents. Keep the **Claude** partner agent off |
| Cross-model review | Pin a different vendor per role. Non-Anthropic models listed in Nov 2026: OpenAI GPT-6.1 Sol/Terra, GPT-6 Sol/Luna/Astra, GPT-5.6 Sol/Luna/Terra, GPT-5.3-Codex, GPT-5.4 nano; Google Gemini 4 Argon, Gemini 3.8 Flash; xAI Grok 4.5–4.7; Moonshot Kimi K3; Microsoft MAI-Code-1.1-Flash. Example: implementer = GPT-6.1 Sol, reviewer = Gemini 4 Argon, optional tie-break = Grok 4.7 or Kimi K3. Check the [live model list](https://docs.github.com/en/copilot/reference/ai-models/supported-models); names change often, and which plans get which model wasn't verified |
| PR review | Copilot code review is one reviewer. It reads `copilot-instructions.md`, path-specific `*.instructions.md`, `AGENTS.md`, skills (e.g. a `code-review` skill) and org instructions. There's no model switching; effort levels are Lite/Balanced/Max (Max in preview), billed in AI credits plus Actions minutes. Your own `reviewer.agent.md` on another model is a separate step; there's no official way to merge the two, but a review skill is the official way to customize it. Its models aren't disclosed, so ask your admin whether Anthropic models can be involved |
| Gates | GitHub Actions, branch protection, required checks |

**Copilot has several surfaces, each with its own admin policy.** Having Copilot doesn't mean you have all of them:

| Surface | What it is | How to check you have it |
|---|---|---|
| IDE chat / agent mode | Copilot inside VS Code or Visual Studio. Custom agents (`.github/agents`) work here | You're using it today |
| **Copilot CLI** | A separate terminal tool (`copilot`), GA since Feb 2026. This is where `/fleet` and headless scripting live | Install it per the [Copilot CLI docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli) and sign in. If the org policy is off, it says so |
| Cloud agent (formerly "coding agent") | Assign an issue to Copilot on github.com → it opens a PR | "Copilot" appears in an issue's Assignees list |
| Third-party coding agents (Agent HQ, public preview) | Codex (and Claude, keep off) as cloud agents | They appear next to Copilot in the agent picker on github.com |
| Copilot code review | Copilot as a PR reviewer | You're using it today |

**Your org's models (as you reported, Nov 2026):** GPT-6.1 (Sol, Terra), GPT-6 Sol/Luna and other recent OpenAI models; not GPT-6 Astra. **Still to check: are any non-OpenAI models enabled (Gemini 4 Argon, Grok, Kimi, MAI)?**
- **If yes:** pin a different vendor for the reviewer, as in the table above.
- **If OpenAI only:** true cross-vendor review isn't possible at work. Get the closest equivalent:
  - Use a different model *tier* for the reviewer than the implementer (e.g. implementer GPT-6.1 Sol, reviewer GPT-6.1 Terra or GPT-6 Sol).
  - Give the reviewer a fresh context and a read-only tool set.
  - Add Copilot code review as an independent second pass.
  - Rely on tests and CI as the real gate.
  - Measure whether this catches defects (§10) rather than assuming it does.

**Phase 0: ask your admin before week 1** (the answers decide how Phase B works at work), only about the ones you don't already have: the Copilot CLI policy, the cloud agent, the Codex partner-agent policy, and any non-OpenAI models. Anthropic models and the Claude agent stay disabled through per-model toggles. The org-level toggle is in public preview, and whether it also covers code review, the cloud agent and the CLI isn't verified (see GitHub's "Supported surfaces for Copilot policies").

Last verified: Nov 2, 2026.

Exercise (work, Phase B; needs the admin answers): weeks 3–4, create `implementer` and `reviewer` agent files pinned to different models and run one real ticket → PR → Copilot code review + your reviewer agent. Weeks 5–6, add a `tester` agent file and run a ticket through `/fleet` if you have the Copilot CLI. Record the §10 metrics.

Exercise (home, Phase B): implement a React list + .NET endpoint feature in two steps.
1. Weeks 3–4 (level 1): you write `spec.md` with the API contract → one implementer in a worktree → Codex reviews the exact commit → you accept/reject each finding → CI merges.
2. Weeks 5–6 (level 2): add a planner subagent that writes the spec, a second implementer in its own worktree, and a tester that writes tests from the spec. Keep each role only if the §10 metrics show it helps against your Phase A baseline.

---

## 4. Working with different models together (Q5)

Phase B uses the cross-model review rows. Phase C adds per-role routing and the evidence comparison.

| Technique | What it is | How to do it |
|---|---|---|
| Cross-model review | Implementer and reviewer use different vendors' models; they make fewer shared mistakes | [openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc): official OpenAI plugin for Claude Code (Mar 2026): `/codex:review`, `/codex:adversarial-review`, `/codex:rescue`. The review gate on stop is off by default (`/codex:setup --enable-review-gate`); the README warns it can loop and drain usage. Works with a ChatGPT plan or API key |
| Per-role model choice | Strong model for planning/review, cheaper/faster model for tests, search, boilerplate | `model:` field in the [subagent](https://code.claude.com/docs/en/sub-agents) file (`sonnet`/`opus`/`haiku`/`fable`, a full ID, or `inherit`; Claude models only, so cross-vendor comes from the row above); model selection per Copilot custom agent |
| Multi-vendor agents on GitHub | Claude and Codex as assignable agents on issues/PRs next to Copilot | GitHub Agent HQ (Pro, Pro+, Business, Enterprise; admin enables each partner agent). **At work: Codex only** (see 3.6) |
| Gemini as a home reviewer | Your Google AI Pro plan works with **Antigravity CLI (`agy`)** (announced on the Google Developers Blog, "transitioning Gemini CLI to Antigravity CLI"), not Gemini CLI. Google announced the move on May 19, 2026; Gemini CLI (and the free Code Assist for individuals tier) stopped serving AI Pro/Ultra on Jun 18, 2026. The binary is `agy` per the [Antigravity CLI docs](https://antigravity.google/docs/cli/headless/) | Use `agy` interactively, or hand it the exact diff manually, as an optional third reviewer. Wrapping or scripting `agy` on a consumer plan has unclear terms, so for scripted use take a paid Gemini API key (Gemini CLI still works with paid Gemini or Gemini Enterprise Agent Platform API keys) |
| Home cross-model reviewer (main) | `openai/codex-plugin-cc`, official, ~35k stars (Nov 2026) | Needs Node 18.18+ and `npm i -g @openai/codex`. Install: `/plugin marketplace add openai/codex-plugin-cc` → `/plugin install codex@openai-codex` → `/reload-plugins` → `/codex:setup`. [Codex pricing](https://learn.chatgpt.com/docs/pricing): Free ($0) and Go ($8/mo) include Codex with no published limits, fine for a trial; Plus ($20/mo) has published 5-hour limits and cloud features; an API key works for CLI use at API rates |
| Provider-neutral API layer | One interface, swap providers by config | .NET: [Microsoft.Extensions.AI](https://learn.microsoft.com/en-us/dotnet/ai/microsoft-extensions-ai) (`IChatClient`, GA, 10.x). Python: [LiteLLM](https://docs.litellm.ai): **two supply-chain compromises in 2026** (PyPI 1.82.7/1.82.8 in Mar; 1.107.0 and Docker `latest`/`main-*` tags in Oct), so pin exact versions with hashes, never use floating Docker tags, and check advisories before upgrading. Hosted router (home): [OpenRouter](https://openrouter.ai/docs) |
| Model comparison by evidence | Same task, same acceptance criteria, different setups | [AI Coding Workflows: From Cloud to Local (DeepLearning.AI + JetBrains)](https://www.deeplearning.ai/courses/ai-coding-workflows-from-cloud-to-local) (home): free to enrol (certificate needs Pro), ~1.5 h, updated Sep 2026; swaps Claude Code ↔ OpenCode, routes via OpenRouter, ends with a local model in LM Studio (small quantized models only on your laptop) |

Two models agreeing doesn't prove the code is correct. Tests and your judgment decide.

---

## 5. Scripted orchestration (Phase D)

Build a small coordinator **around existing harnesses**; don't rewrite the agent loop. Weeks 9–10: the coordinator at home. Weeks 11–12: the Hermes trigger (§5.1 exercise) and the work variant.

**Work variant (Copilot only):** not yet written; it's waiting on a research-topic pass (see [validation-log.md](validation-log.md)).

```text
task intake → validate scope → git worktree add → launch implementer (headless, JSON output)
→ collect result + commit SHA → run tests/CI → launch reviewer on that SHA
→ ≤2 repair rounds → stop for human approval
```

Your coordinator must handle: task IDs and state, exit codes, timeouts, cancellation, retry limits, commit identity (re-review after any change), cost/concurrency caps, secrets kept out of worker env, escalation to you.

| Resource | Use |
|---|---|
| [Claude Code headless / `-p`](https://code.claude.com/docs/en/headless) | `--output-format json` (incl. `session_id`, `total_cost_usd`), `--json-schema`, `--resume`, permission flags. Since v2.1.302 `-p` starts in Manual mode; use `--permission-prompts none` for unattended runs |
| [Codex non-interactive (`codex exec`)](https://learn.chatgpt.com/docs/non-interactive-mode) | Same for Codex: `--json`, `--output-schema`, `exec resume`. `--full-auto` is gone; use `--sandbox workspace-write`. API-key auth for automation |
| [Claude Agent SDK (Python/TS)](https://code.claude.com/docs/en/agent-sdk/overview) | Claude Code's harness as a library: subagents, hooks, MCP, permissions in code |
| [OpenAI agent runtime options](https://developers.openai.com/api/docs/guides/agents) | Three runtimes: Agents API (managed Codex harness, beta; OpenAI runs the loop and stores the session) vs Agents SDK (your app runs the loop, your storage) vs Responses API (you manage state) |
| Optional: [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview) | Anthropic's hosted harness (beta, API-key billed, home only), the counterpart to OpenAI's Agents API. Read it as a concept; it isn't a core step |

Note on billing (watch: this changed twice in 2026): signed in with a Claude plan, `claude -p` and Agent SDK usage still draws from your subscription limits; the split planned for Jun 15, 2026 was paused. Runs with an API key are billed at API rates; since Oct 7, 2026, Max/Ultra/Team plans include monthly API credits for this, and none is listed for Pro. `--bare` is recommended for scripts and will become the `-p` default. It skips OAuth and all auto-discovery (hooks, skills, MCP, CLAUDE.md), so it needs `ANTHROPIC_API_KEY` or an `apiKeyHelper`.

### 5.1 Hermes Agent on your VPS: always on, not on the home PC (validated Nov 2, 2026)

Use Hermes as the Telegram front door: kicking off jobs, notifications, triage, cron reports. Don't make it the code orchestrator. It ships bundled `claude-code` and `codex` skills (`claude -p` or tmux) and an opt-in beta Codex App-Server Runtime, so it *can* dispatch to Claude Code or Codex. You can use those, or trigger `claude -p --output-format json` / `codex exec` through your own script and report the result back. It supports MCP, agentskills.io skills and cron delivered to Telegram (`deliver: telegram`; the gateway must be running). Subagents share one delegation model/provider by default; per-task routing needs the `advanced-delegation` plugin (`delegation.backend: model_map` or `profile_routes`, v0.22.5+). It reads project context files, first match wins: `.hermes.md` > `AGENTS.override.md` > `AGENTS.md` > `CLAUDE.md`. Only one type loads, so a repo with both `AGENTS.md` and `CLAUDE.md` gives Hermes only `AGENTS.md`. Current: v0.22.6 (Nov 2, 2026; releases every few days).

**VPS size:** Hermes publishes no hardware minimums. Hetzner's cheapest listed plan is CX23 (2 vCPU, 4 GB, €5.99/mo excl. VAT), but it showed as unavailable on Nov 2, 2026. Avoid 1 GB plans for gateway + Docker. **Yours is a CX23 (2 vCPU, 4 GB, 40 GB),** which should fit Hermes plus light Docker jobs (an estimate, not a published figure). Measure it with `free -m` and `docker stats` before adding work.

**Two ways to start coding work from Telegram:**

| Route | How | Notes |
|---|---|---|
| A. Cloud trigger (preferred, laptop can be off) | Hermes makes one HTTP call to a [Claude Code cloud scheduled task](https://code.claude.com/docs/en/routines) (formerly "routines") API trigger: `POST …/v1/claude_code/routines/<id>/fire`. Or it posts an `@claude` comment or a `claude` label on a GitHub issue, which [claude-code-action](https://github.com/anthropics/claude-code-action) (`@v1`) picks up | Scheduled tasks are a **research preview** (Pro and above; beta header `experimental-cc-routine-2026-04-01`; API may change). Limits: 30 runs/h per task (shared with Run now) and 100 API fires/h per account. Only a per-task token sits on the VPS; it's created and rotated on the web only. Text you send arrives as an untrusted payload, so the task's saved prompt must say to act on it. Cloud runs always bill your subscription. **claude-code-action doesn't open a PR by default:** it pushes to a `claude/` branch and posts a pre-filled PR link. The poster needs write access, and comments made with `GITHUB_TOKEN` don't trigger workflows, so Hermes posts with a fine-grained PAT (acting as you, no `allowed_bots` needed) or a GitHub App (then list it in `allowed_bots`). For Codex in Actions, use [openai/codex-action](https://github.com/openai/codex-action) (`@v1`, v1.13 in Oct 2026): it documents only an OpenAI or Azure OpenAI API key |
| B. Run CLIs on the VPS | `claude -p` / `codex exec` in Hermes's **`docker`** terminal backend (`terminal.backend: docker`), with the CLIs baked into `terminal.docker_image` (default `nousresearch/hermes-sandbox:desktop`) | Fine for light jobs (summaries, review of a small diff). On a minimal Hetzner box, don't plan full .NET + React builds and tests. Any variable in `terminal.docker_forward_env` can be read and exfiltrated by code in the container, so forward only the one token the job needs |

Skip Remote Control for this (the host has to stay on with `claude` running, and setup-token tokens can't open sessions), and don't point Hermes's `ssh` backend at the laptop (it may be asleep).

**Auth on the VPS:**
- **Claude:** `claude setup-token` creates a one-year `CLAUDE_CODE_OAUTH_TOKEN` (Pro, Max, Ultra, Team or Enterprise). Anthropic documents it "for CI pipelines and scripts".
  - It's for model requests only: it can't open Remote Control sessions or fetch claude.ai connectors, and `--bare` doesn't read it. Locally configured MCP servers still work.
  - Run unmodified `claude -p` for yourself only.
  - **Never set that token as Hermes's own model provider.** Anthropic explicitly forbids routing plan credentials through third-party apps. It explicitly allows signing in to the unmodified Claude Code binary with your own plan, and the Consumer Terms (s.3(7)) ban bot or script access "except … via an API Key or where we otherwise explicitly permit it". A personal bot running unmodified `claude -p` isn't named either way (watch). Its closest match is the permitted "scripts" use. For heavy always-on automation, use an API key.
  - A stray `ANTHROPIC_API_KEY` (or `ANTHROPIC_AUTH_TOKEN`) overrides the OAuth token and silently switches you to API billing.
  - Without `--bare`, a `-p` run executes the repo's hooks and `.mcp.json` servers with no trust prompt. Subscription runs can't use `--bare`, and `--bare` will become the `-p` default in a future release, so use trusted repos or an isolated clone only. Watch for how subscription runs work once the default flips.
- **Codex ([auth docs](https://learn.chatgpt.com/docs/auth)):** `codex login --device-auth` (beta) or `codex login --with-api-key`. OpenAI recommends API keys for automation; ChatGPT tokens are for trusted private runners only.

**Security checklist (Phase 0: your Hermes already runs, so do this now, before giving it shell or GitHub access; maps to OWASP Agentic ASI01, 02, 03, 05, 06, 08):**
- **Stay on the latest release** (v0.22.2 at minimum). 2026 brought several CVEs, including a webhook auth bypass (CVE-2026-114463, High), a bundled-MCP-catalog supply-chain issue (CVE-2026-82021, Critical) and an RCE (CVE-2026-71963, High). These are mostly unreviewed, auto-imported GitHub advisory entries, and the repo itself lists none, but they line up with real fixes in the release history. Check [GitHub advisories for hermes-agent](https://github.com/advisories?query=hermes-agent) on every update.
- Set `TELEGRAM_ALLOWED_USERS=<your numeric id>` (with no allowlist, everyone is denied by default). Never set `GATEWAY_ALLOW_ALL_USERS` or `*`.
- Set `approvals.mode: manual`, or `smart` (the default) plus quoted `approvals.deny` globs for `git push --force`, repo deletion and similar. Never use `off` or YOLO (`--yolo`, `/yolo`, `HERMES_YOLO_MODE=1`).
- Use the Docker terminal backend, keep `terminal.docker_forward_env` minimal, and run `chmod 600 ~/.hermes/.env`.
- Give the PAT the least scope possible: fine-grained, limited to the repos it needs.
- Treat Telegram messages, issue bodies and PR comments as untrusted input. Prompt injection is ASI01.
- No work code or work credentials on the VPS.

Exercise (VPS, Phase D weeks 11–12): from Telegram, have Hermes open an issue with an `@claude` comment on a pet-project repo → claude-code-action pushes a `claude/` branch with a PR link → Hermes reports the link back. Then do the same with a scheduled-task `/fire` call.

---

## 6. Building your own agents (Phase E)

### 6.1 Foundations (pick in this order)

Work through §6 in order: 6.1–6.5 feed the capstone, 6.6 RAG comes next, and 6.7 is optional.

| # | Item | Cost · time | What you get |
|---|---|---|---|
| 1 | [Agentic AI, Andrew Ng (DeepLearning.AI)](https://www.deeplearning.ai/courses/agentic-ai/) | Audit free; graded work Pro ($25–30/mo) · ~10 h | Reflection, tool use (incl. MCP), planning, multi-agent, error analysis in Python, from first principles before frameworks |
| 2 | Optional, home only: [Building with the Claude API (Skilljar)](https://anthropic.skilljar.com/claude-with-the-anthropic-api) | Free · ~8 h (not stated on page) | Tool use, structured output, RAG, prompt caching. Overlaps #1 on the agent loop, so take it only if you build on the Claude API at home |
| 3 | [Writing effective tools for agents, Anthropic](https://www.anthropic.com/engineering/writing-tools-for-agents) (Sep 2025; evergreen) | Free · 20 min read, 1 h with applying it | Tool naming, descriptions, response shaping: the most common cause of bad agents |

### 6.2 .NET first: Microsoft Agent Framework (your highest-leverage build skill)

Successor to Semantic Kernel + AutoGen (the AutoGen repo is now archived). GA since Apr 3, 2026 for .NET and Python (Nov 2026: .NET `Microsoft.Agents.AI` 1.27, Python `agent-framework` 1.24). Built-in orchestrations: sequential, concurrent, handoff, group chat, Magentic (Magentic is still experimental in .NET).

| Item | Cost · time |
|---|---|
| [Agent Framework overview](https://learn.microsoft.com/en-us/agent-framework/overview/) + [orchestrations](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations) | Free · 3 h |
| [Develop AI agents on Azure (MS Learn path AI-3026)](https://learn.microsoft.com/en-us/training/paths/develop-ai-agents-azure/) | Free (exercises need an Azure subscription) · ~10 h (not stated on page); uses current Foundry Agent Service + MAF naming |
| [Generative AI for Beginners .NET](https://github.com/microsoft/generative-ai-for-beginners-dotnet) | Free · 5–8 h: MAF, Microsoft.Extensions.AI, RAG |
| [.NET AI docs](https://learn.microsoft.com/en-us/dotnet/ai/overview) | Reference |

Check package status: the core packages are stable, but some integrations are still beta (Python `-a2a`, `-azure-ai-search`, `-copilotstudio`; .NET `Microsoft.Agents.AI.Foundry` is on a preview line), and DevUI, hosted agents and Skills are preview. Pin versions. (Last verified: Nov 2, 2026.)

### 6.3 MCP (tools) and A2A (agent ↔ agent)

| Item | Cost · time | Why |
|---|---|---|
| [MCP spec & docs](https://modelcontextprotocol.io/docs/getting-started/intro) · [C# MCP SDK](https://github.com/modelcontextprotocol/csharp-sdk) · [Claude Code MCP](https://code.claude.com/docs/en/mcp) (home only; SSE deprecated, use HTTP) | Free | **Current spec: 2026-07-28.** It's stateless (no `initialize` handshake), adds `server/discover` and multi-round-trip requests, and deprecates Roots, Sampling, Logging and HTTP+SSE. C# SDK v2.3 (stable; 2.0 added the new spec); Python `mcp` 2.x. Pin to the 2.x SDKs and expect older tutorials to break. Build servers in .NET, use them from every harness |
| [Introduction to MCP (Anthropic Academy)](https://anthropic.skilljar.com/introduction-to-model-context-protocol) (home) | Free · ~2 h (not stated officially) | Tools/resources/prompts, Inspector, a client. Written before spec 2026-07-28, so check it against the spec |
| Optional: [MCP Advanced Topics](https://anthropic.skilljar.com/model-context-protocol-advanced-topics) · [Hugging Face MCP Course](https://huggingface.co/learn/mcp-course/en/unit0/introduction) | Free · ~3 h · ~12–20 h | Both predate spec 2026-07-28. Advanced Topics teaches Roots/Sampling/Logging, now deprecated, so skip those lessons. HF's certificates were offered, but current issuance is unverified |
| [A2A protocol](https://a2a-protocol.org) | Free · 2 h | v1.0 (Mar 2026; breaking vs 0.3), under the Agentic AI Foundation (Linux Foundation) since Aug 2026; Agent Cards, tasks, streaming; official .NET SDK. Learn once agents live in separate services |

Last verified: Nov 2, 2026.

### 6.4 Evals and observability (the skill that separates demos from production)

| Item | Cost · time | Why |
|---|---|---|
| [Evaluating AI Agents (DeepLearning.AI + Arize)](https://www.deeplearning.ai/short-courses/evaluating-ai-agents/) | Free during DLAI beta (certificate needs Pro) · ~2.6 h + practice | Traces, component vs end-to-end evals, LLM-as-judge. Labs use Arize Phoenix (home) |
| [AI Evals FAQ, Hamel Husain & Shreya Shankar](https://hamel.dev/blog/posts/evals-faq/) | Free · 2 h | Error analysis first, then judges; the free core of the paid course (updated Sep 2026) |
| [AI Evals for Engineers & PMs (Maven)](https://maven.com/parlance-labs/evals) | $4,400 (discount codes circulate) · 3–5 h/wk × 8 wk; next cohort Jan 30 – Mar 27, 2027 | Deep version; ask your employer to pay. Optional |
| [OpenTelemetry GenAI semantic conventions](https://github.com/open-telemetry/semantic-conventions-genai/blob/main/docs/gen-ai/README.md) | Free · 1 h | Standard trace format for LLM/agent calls, incl. agent spans and MCP. Moved to its own repo in 2026; status Development, so expect attribute renames |
| [Langfuse docs](https://langfuse.com/docs) (open source) or LangSmith | Free tier (Hobby: 50k units/mo, 30-day data); self-host free | Trace viewer, datasets, eval runs. Owned by ClickHouse since Jan 2026; core stays MIT. At work, check company policy first |
| [Aspire dashboard (standalone)](https://aspire.dev/dashboard/standalone/) | Free (MIT) · one Docker container (`mcr.microsoft.com/aspire/dashboard`, UI :18888, OTLP :4317/:4318) | **Work-side default** trace viewer for .NET: OTel traces with a GenAI visualizer for spans that carry GenAI semconv attributes (since Aspire 9.5; current 13.6). The docs don't say explicitly that the standalone image includes the visualizer, so confirm it hands-on |

### 6.5 Security (do this before agents get shell/git/network access, so in Phase B)

- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) (Dec 2025): ASI01 agent goal hijack, ASI02 tool misuse, ASI06 memory & context poisoning, and more; guiding principle "least agency".
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/) (2025 edition, current; evergreen): LLM01 prompt injection is still #1; also read LLM06 excessive agency.
- Optional: [Red Teaming LLM Applications (DeepLearning.AI)](https://www.deeplearning.ai/short-courses/red-teaming-llm-applications/): free, ~1.7 h, built on Giskard and chatbot-focused. Skip it if you read the OWASP lists or take the Maven course.
- Practical rules: least-privilege tools, no production secrets in agent env, reviewer read-only via permissions (not via the prompt), treat issues/docs/tool output as untrusted input, cap retries and spend.

### 6.6 RAG (only when an agent needs private or changing knowledge)

- [Retrieval Augmented Generation (DeepLearning.AI)](https://www.deeplearning.ai/courses/retrieval-augmented-generation-rag/): free audit (certificate paid) · ~26 h video, so take modules 2, 3 and 5 only: BM25 + vector hybrid search, chunking, reranking, retrieval and generation evals. RAG is in about 40% of AI-engineer postings (section 11).

§6.4–6.6 last verified: Nov 2, 2026.

### 6.7 Python: LangGraph, yes or no? (Q8, optional)

- **Still relevant.** LangGraph 1.2.x (stable) is the most-downloaded Python agent-orchestration framework (~45M PyPI downloads/month in Nov 2026, vs ~12M for the OpenAI Agents SDK), and it appears in ~4.3k US AI job postings in 2025, up from 194 in 2024 (Stanford AI Index 2026, Lightcast data). [Thoughtworks Radar Vol. 34](https://www.thoughtworks.com/radar/languages-and-frameworks/langgraph) (Apr 2026) moved it from Adopt to Trial: "no longer the default choice for building every agentic system".
- **Not needed for Phases A–D** or the Phase E capstone. Coding-agent teams run on Claude Code / Codex / Copilot, not on LangGraph.
- **Learn it if** you build stateful Python agent services (checkpoints, human-in-the-loop, durable execution).
- **Which course:** [Quickstart: LangGraph Essentials (Python)](https://academy.langchain.com/courses/langgraph-essentials-python) (free, ~1 h, 13 lessons) + [LangGraph docs](https://docs.langchain.com/oss/python/langgraph/overview). The longer "Introduction to LangGraph" (free, ~6 h) is an optional deep dive.
- **Alternative:** [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) (lighter, handoffs built in; v0.24, still pre-1.0). Pick **one** Python framework.

Last verified: Nov 2, 2026.

Phase E capstone: a .NET MCP server over one internal API → an Agent Framework agent using it → 30 eval cases in CI → OTel traces in Aspire/Langfuse → OWASP checklist passed.

---

## 7. Running models locally (Q6)

Phase C uses §7.2–7.3 for one small local model in a background job. The rest of §7 is reference for Phase F, which serves its fine-tuned model this way.

### 7.1 What to learn

| Topic | Why it matters |
|---|---|
| Quantization (GGUF Q4/Q5/Q8) | Decides whether a model fits your RAM/VRAM; [GGUF on Hugging Face](https://huggingface.co/docs/hub/gguf) |
| VRAM math | Q4 ≈ 0.6 GB per billion params + KV cache (~0.5–2 GB at 16–32K for small models); keep the model under ~60% of RAM. **Your 16 GB laptop (shared with the iGPU, ~9–10 GB usable):** up to 8–9B dense at 16–32K context, 14B only with short context. 32 GB: up to ~14B comfortably, ~30B dense or MoE is tight. For MoE models, the total parameter count sets memory size and the active parameters set speed |
| Speed on your laptop | Memory bandwidth (~51 GB/s DDR4) is the limit: ~8–12 tok/s for a 4B Q4, ~4–7 tok/s for an 8–9B Q4 (estimates from similar chips; measure with `ollama run <model> --verbose`) |
| Context length | Local servers often default low; coding agents need ≥32–64K |
| Tool-calling support | Not every open model calls tools reliably; agents need it |
| Licensing | Open-weight ≠ unrestricted commercial use; read the model license |

### 7.2 Tools

| Tool | Use |
|---|---|
| [Ollama](https://docs.ollama.com/) | **Your default** (v0.40.x). Easiest server; OpenAI- **and** Anthropic-compatible API. Vulkan is on by default, but Ollama skips integrated GPUs, so Iris Xe is used only if you set `OLLAMA_IGPU_ENABLE=1`. On weak iGPUs it's sometimes slower than CPU: benchmark both (`ollama ps`, tokens/s) and keep whichever wins |
| [LM Studio](https://lmstudio.ai/docs/app) | GUI + local server (ships llama.cpp runtimes incl. Vulkan; the iGPU is disabled by default since 0.4.17, so enable it in runtime settings); used in the DeepLearning.AI cloud-to-local course |
| [llama.cpp](https://github.com/ggml-org/llama.cpp) | The engine under most local tools; max control; Windows Vulkan and SYCL (Intel GPU) builds |
| [OpenVINO 2026.4](https://docs.openvino.ai) | Intel's own runtime (CPU/iGPU/NPU), OpenAI-compatible via Model Server (Windows binaries). Optional experiment on your Intel laptop. (IPEX-LLM was archived Jan 2026 with known security issues, so don't use it) |
| Optional: [vLLM](https://docs.vllm.ai) | High-throughput serving on a GPU server (team/production use); no native Windows, so not for your laptop. Useful later on rented GPUs |

**Models for your laptop (Nov 2026, Q4_K_M sizes):** `qwen3.5:4b` (3.3 GB; main choice for tool calling and JSON), `qwen3.5:9b` (6.6 GB; best quality that fits), `gemma4:e4b` (6.6 GB; second opinion, multimodal). Set `num_ctx` to 16384–32768 explicitly. `gpt-oss:20b` (14 GB) doesn't fit in 16 GB with Windows running.

**Hardware:** don't buy now. Prices are inflated in 2026, and $50–100 of cloud GPU covers a year of learning. The RTX 5090 has a $1,999 MSRP but sold for ~$3.7K–6K+ on the street in Aug–Oct 2026, often out of stock (secondary sources only). NVIDIA raised the DGX Spark 128 GB Founders Edition to $4,699 in Feb 2026; Oct 2026 reports of $6,950 aren't confirmed. A cheap optional step is upgrading the T14 Gen 2's RAM to 32–40 GB, which allows slow 14B-class or MoE models. Confirm the slot layout on Lenovo PSREF first.

### 7.3 Local models inside your coding-agent team

- **Claude Code + local model (home):** [Ollama Anthropic compatibility](https://docs.ollama.com/api/anthropic-compatibility) (Ollama ≥ 0.14). Set `ANTHROPIC_BASE_URL=http://localhost:11434`, `ANTHROPIC_AUTH_TOKEN=ollama`, `ANTHROPIC_API_KEY=""`, then `claude --model <tag>`, or use `ollama launch claude`. Values in Claude settings files can override these env vars. Hosted web search isn't fully supported, and neither are `tool_choice`, prompt caching, Batches or PDF blocks. **On your laptop, do this once to learn the setup, not as a live subagent.** Each turn first processes a 20–50K-token prompt, which takes minutes here, and 9B models edit poorly; ~20–27B is the practical minimum for agentic coding.
- **Codex / OpenAI-compatible tools:** point them at Ollama/LM Studio's OpenAI-compatible endpoint.
- **.NET apps:** `IChatClient` from Microsoft.Extensions.AI with the Ollama provider, so a config switch swaps local and cloud.
- **Realistic use:** on your laptop, local models fit offline batch jobs: summarizing logs and docs, commit-message and docstring drafts, test stubs generated by a background script, embeddings/RAG experiments, and learning the API and tool calling. Keep frontier models for every interactive role. Measure this; don't assume it.
- Local inference ≠ private by default: plugins, telemetry and MCP servers can still send data out.
- A CPU-only VPS isn't faster than your laptop; its only advantage is being always on. Use it at most for tiny always-on helpers for Hermes (classification, short summaries). Don't rent a VPS for inference.

Phase C exercise (home): run the same Phase-B task under three setups. Keep the Phase B cross-model reviewer in all of them (Codex via codex-plugin-cc, optionally Gemini via `agy`).
- (a) One model for every role.
- (b) Per-role routing (§4): the strongest model for planning and review, a cheaper one for tests, search and boilerplate.
- (c) Setup (b) plus a local `qwen3.5:4b` generating test stubs as a background script.

Compare accepted-change time, defects and cost. For a local model as a real interactive role, rent a GPU for a few hours and serve a ~27B model instead.

§7 last verified: Nov 2, 2026. Watch: newly posted small tool-capable models (nemotron-3.5-bolt e4b, nimble-v3 9b, bonsai-2 4b/9b) have no independent evals yet.

---

## 8. Building and adapting models (Q7)

Practice comes first: steps 1–2 (~12–17 h) meet the Phase F exit criterion. Steps 3–6 add depth; take them once you have a working adapter. Each step has a concrete output.

| Step | Resource | Cost · time | Output |
|---|---|---|---|
| 1. Post-training theory | [Post-training of LLMs (DeepLearning.AI)](https://www.deeplearning.ai/short-courses/post-training-of-llms/) | Free during DLAI beta (certificate needs Pro) · ~1.5 h | When to use SFT vs DPO vs RL |
| 2. Practical fine-tuning | [Unsloth docs](https://unsloth.ai/docs) (LoRA on a single consumer GPU or free Colab; Unsloth Studio is a browser UI) + [Hugging Face TRL](https://huggingface.co/docs/trl) (v1.x: SFT, DPO, GRPO stable; ORPO/CPO experimental) | Free · 10–15 h | A LoRA adapter on your own dataset, exported to GGUF and served by Ollama |
| 3. How LLMs work, in code | [Neural Networks: Zero to Hero, Andrej Karpathy](https://karpathy.ai/zero-to-hero.html) (evergreen) | Free · ~14.5 h video, 20–30 h with coding | You write a GPT from scratch in PyTorch |
| 4. Transformers ecosystem | [Hugging Face LLM Course](https://huggingface.co/learn/llm-course/chapter1/1) | Free · ~36–48 h for ch. 1–3 + 10–12 (full course ~72–96 h) | Tokenizers, datasets, fine-tuning with `transformers` |
| 5. Full training run (optional) | [karpathy/nanochat](https://github.com/karpathy/nanochat) | ~$48 of rented GPU (~2 h on 8×H100 at ~$3/GPU-h; ~$15 on spot) | Tokenizer → pretrain → SFT → RL → chat UI, end to end |
| 6. Research depth (optional) | [Stanford CS336: Language Modeling from Scratch](https://cs336.stanford.edu/) (Spring 2026 offering; videos on YouTube, assignments on GitHub) | Free materials · 100+ h; assignments need GPU time (Modal gives $30/mo free) | Needs linear algebra, probability, PyTorch, GPU budget |

Before fine-tuning, always have (a) a prompt + RAG baseline and (b) a held-out eval set. Fine-tune only when it measurably beats the baseline. Fine-tuning appears in about 15% of AI-engineer postings, while agents/RAG/evals appear far more often (section 11). Treat this track as a long-term investment, not a job requirement.

**Where to run it (your laptop can't fine-tune):**
- **First, your Google AI Pro plan:** it includes 200 [Colab](https://one.google.com/about/google-ai-plans/) compute units a month with better GPUs and longer runtimes. Use these before renting anything.
- **Free tiers:** Unsloth's free notebooks on Colab (usually a T4 with 16 GB, not guaranteed; sessions up to 12 h) or Kaggle (P100 or 2×T4; ~30 GPU-h/week by community reports, since Kaggle doesn't publish the quota).
- A LoRA run on a 4–8B model with ~1k examples takes 15–60 min, which is $0 there.
- **If you run out of memory:** rent on [RunPod](https://www.runpod.io/pricing) (Oct 2026 prices): an RTX 4090 costs $0.34/h Community or $0.89/h Secure, and an L40S costs $0.79/h or $1.09/h. That's under $5 per experiment.
- Export to GGUF and serve with `ollama create` to connect this track to §7.

Phase F exercise: fine-tune a 3–8B model (e.g. Qwen3.5-4B in bf16 LoRA, ~10 GB VRAM; Unsloth advises against 4-bit QLoRA for Qwen3.5) with Unsloth on ~1,000 examples from your domain (e.g. your codebase's commit-message or API-doc style), serve it with Ollama, compare it with the base model on your eval set.

§8 last verified: Nov 2, 2026.

---

## 9. Reusable templates (copy into your repo, e.g. `docs/agents/`)

### Implementation assignment
```text
Goal: Implement [behavior].
Acceptance criteria:
- [observable behavior]
- [error/edge case]
- [compatibility requirement]
Scope: own [files/components]; do not modify [areas]; coordinate before changing [contract].
Constraints: follow existing patterns; no new dependencies without justification;
do not weaken tests; stop on unresolved product/architecture decisions.
Verification: run [commands]; report checks not run and why.
Return: commit SHA, behavior changed, commands + results, risks/assumptions/blockers.
```

### Reviewer assignment (run with read-only permissions)
```text
Review commit [SHA] against: [acceptance criteria].
Inspect the actual code and existing behavior. Do not trust any summary. Do not edit files.
Per finding: file:line, trigger condition, wrong behavior + impact, evidence/repro.
Separate confirmed findings from uncertainties. Return "no findings" if none are supported.
```

### Test assignment
```text
Derive tests from the spec, public behavior and failure modes (not from the implementation).
Follow existing test conventions. Cover: normal path, boundaries/invalid input,
permissions, integration where relevant. Never remove or weaken assertions to pass.
Report missing coverage and environment limits.
```

### Handoff record
```text
Task: | Owner: | Base commit: | Candidate commit:
Scope changed: | Acceptance criteria addressed:
Checks executed: | Checks NOT executed:
Contract changes: | Known issues: | Decision needed:
```

### Role file example (`.claude/agents/reviewer.md`)
```markdown
---
name: reviewer
description: Reviews a given commit against acceptance criteria. Use after implementation.
tools: Read, Grep, Glob, Bash(git diff:*), Bash(git show:*), Bash(dotnet test:*)
model: opus
---
You review code. You never edit files. Follow docs/agents/reviewer-assignment.md.
```

---

## 10. Measuring whether the team helps

| Metric | Record |
|---|---|
| Time to accepted change | Planning + agent runtime + review + repair + integration |
| Human intervention | Minutes spent correcting, re-prompting, fixing |
| Correctness | Criteria met, regressions, defects found later |
| Review quality | Useful findings vs false positives vs missed issues |
| Integration cost | Conflicts, contract mismatches, duplicated work |
| Cost per accepted task | Tokens/$ including failed runs and retries |

Improvement loop: baseline with one agent → classify failures (unclear spec, missing context, wrong tool use, bad code, weak tests, integration) → change one variable → repeat on similar tasks → keep the change only if it's measurably better. Adding an agent is one option among many.

Monthly: read release notes of your harnesses, re-run 3 benchmark tasks before switching model/config, delete stale instructions. Quarterly: check whether parallelism still saves effort; review credentials/sandbox.

---

## 11. Market demand evidence (why these items)

| Signal | Source |
|---|---|
| AI agents in ~47% of AI postings (#3 skill, behind ML 59% and LLMs 56%); RAG ~40% (4,155 postings, Jun 2026). The same author's May 2026 cut of the same job board: Python 71%, LLMs 66%, RAG 40%, Azure 33%, monitoring 31%, LangChain 25% (3,449 postings). One job board's own data, so it counts as one source | [InterviewStack, Jun 2026](https://interviewstack.io/blog/how-ai-is-changing-ai-engineer-2026) · [DEV cross-post, May 2026](https://dev.to/gnana_6392e836fd500a957dc/ai-engineer-skills-companies-want-in-2026-3449-posting-analysis-5f9l) |
| 2025 US postings mentioning the term: "agentic AI" 16.5k (from 151 in 2024), RAG 12.6k, LangGraph 4.3k (from 194). Agentic-AI skills overall went from 0.06% to 0.23% of US postings (~90k) | [Stanford AI Index 2026, pp. 204–208](https://hai.stanford.edu/assets/files/ai_index_report_2026.pdf) (Lightcast data) · [Lightcast summary](https://lightcast.io/resources/blog/stanford-ai-2026) |
| US AI Engineer hiring +64% YoY (last quarter) while overall US hiring fell 6.9% | [LinkedIn AI Labor Market Update, Aug 2026](https://delivery-p143253-e1476319.adobeaemcloud.com/adobe/assets/urn:aaid:aem:ef153078-1061-4817-82e7-a1c027d7a7d7/original/as/AI-Labor-Market-Update-August-2026-v2.pdf) |
| 57% have agents in production; 89% use observability; 52% run offline evals (fielded Nov–Dec 2025; respondents lean toward the LangChain ecosystem) | [LangChain State of Agent Engineering](https://www.langchain.com/state-of-agent-engineering) (n=1,340) |
| MCP and agent mode are now on the GitHub Copilot certification exam | [GH-300 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/gh-300) (skills as of Aug 2026) |
| Fine-tuning in ~15% of postings; no evidence employers require CCA-F, AI-103 or GH-300 | Claude roadmap validation |
Numbers differ between sources (different boards and taxonomies), so treat them as direction, not precision. The consistent ranking is: **agents + LLM integration > RAG > cloud (Azure) > observability/evals > MCP > frameworks > fine-tuning > certificates.**

Multi-agent coding orchestration specifically is a **growing differentiator, not a baseline hiring requirement**. AI coding agents are mainstream at work: [JetBrains Developer Ecosystem 2026](https://blog.jetbrains.com/research/2026/08/ai-coding-agent-adoption-2026/) (15k+ developers) found 90% of professional developers use them at least weekly and 68% daily. But [State of Agentic Coders Q1 2026](https://nikolasburk-com.pages.dev/blog/state-of-agentic-coders-results/) (n=199, self-selected, directional only) found most developers don't run multi-agent orchestration yet. Build it on top of solid single-agent skills: context files, evals, review gates.

Last verified: Nov 2, 2026.

### Certificates (optional; only if your employer values them)

| Cert | Status (Nov 2026) |
|---|---|
| [GH-300 GitHub Copilot](https://learn.microsoft.com/en-us/credentials/certifications/github-copilot/) | Covers agent mode, MCP, sub-agents, Copilot CLI, code review (study guide Aug 2026). Free prep: MS Learn "GitHub Copilot Fundamentals" Part 1/2; [DataCamp track](https://www.datacamp.com/tracks/github-copilot-fundamentals-gh-300) optional (subscription). Best fit for your work setup |
| [AI-103: Developing AI Apps and Agents on Azure](https://learn.microsoft.com/en-us/credentials/certifications/azure-ai-apps-and-agents-developer-associate/) | Live since ~Jun 2026 (leads to Azure AI Apps and Agents Developer Associate); replaced AI-102 (retired Jun 30, 2026). Python + Foundry. Fits if your employer is Azure-heavy |
| Optional, home only: [Claude Certified Architect – Foundations](https://anthropic-partners.skilljar.com/claude-certified-architect-foundations-certification) | $125 on Anthropic's Partner Academy; eligibility unverified. Doesn't help at work |

Pick at most one, after Phase E: GH-300 or AI-103.

---

## 12. What changed vs the two source files

| Topic | Decision | Reason |
|---|---|---|
| Codex inside Claude Code | Included (codex-plugin-cc) | Official OpenAI plugin since Mar 2026; the Copilot file missed it |
| Cross-model review | Default, but measured | Claude file: "deliberate"; Copilot file: "no universal rule". Both apply |
| LangGraph | "Essentials" course, Python-only, after MAF | Old intro course predates v1; not needed for coding-agent teams |
| MCP | Phase E (build), used from Phase A | Needed for building; just consumed while operating |
| Evals | Free DeepLearning.AI + Hamel's FAQ first; Maven optional | Maven costs $4,400 (Nov 2026); free material covers the core |
| Local models | Own phase (C) + Ollama → Claude Code integration | Claude file had almost nothing on local inference |
| Building models | Post-training → Unsloth/TRL → Karpathy → HF → nanochat → CS336 | Neither file had a hands-on path from zero to a fine-tuned model; practice comes first so the Phase F exit is reachable in weeks |
| Handoff / communication | Explicit mechanisms + Spec Kit + templates | Your main gap; neither file answered it directly |
| Security | Moved to Phase B | Agents get shell/git access from the start |
| Windows | Native; WSL2 only for agent-team split panes | Resolves the two files' disagreement |

---

## 13. Completion checklist

**Prerequisites (Phase 0)**
- [ ] Hermes on the latest release (v0.22.2 at minimum) and the §5.1 security checklist passed
- [ ] §3.6 admin questions sent; answers recorded in PROGRESS.md
- [ ] OpenAI access for codex-plugin-cc decided (API key or ChatGPT plan)

**Single agent (Phase A)**
- [ ] Repo has CLAUDE.md / AGENTS.md / copilot-instructions.md with exact build/test commands
- [ ] Two skills committed
- [ ] Baseline: §10 metrics for ≥3 single-agent tasks

**Coding-agent team (Phase B)**
- [ ] Implementer and reviewer role files committed; tester and planner only if the metrics justified them
- [ ] Reviewer is read-only via permissions, not via prompt
- [ ] Parallel work uses worktrees with separate ports/DBs
- [ ] Every handoff names an exact commit and lists checks not run
- [ ] Cross-model review set up: home = codex-plugin-cc (optionally Gemini via `agy`); work = role agents pinned to different non-Anthropic models + Agent HQ Codex
- [ ] Hooks/CI block merges on failing checks
- [ ] §6.5 practical security rules applied to agent permissions and environment
- [ ] Metrics recorded for ≥5 tasks: team vs the Phase A baseline

**Multi-model + local (Phase C)**
- [ ] Same task run under 2–3 model setups; cost and quality compared
- [ ] One local model running an offline batch job, with its output measured

**Scripted orchestration (Phase D)**
- [ ] A script runs implement → test → review with timeouts and retry limits
- [ ] One job started from Telegram via Hermes, with the result reported back

**Building agents (Phase E)**
- [ ] Wrote an agent loop without a framework (Claude/OpenAI API)
- [ ] Built and used a .NET MCP server
- [ ] Built an Agent Framework workflow using at least two orchestration patterns
- [ ] 30-case eval suite in CI; traces visible in OTel tooling
- [ ] OWASP agentic Top 10 reviewed against your agent

**Models (Phase F)**
- [ ] Ran 3 local models and compared them on your eval set
- [ ] Fine-tuned a LoRA adapter, served it via Ollama, measured vs base
