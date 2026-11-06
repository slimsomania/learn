# Generalized Approach: AI Agent Engineering Roadmap (Nov 2026)

Prepared: November 2, 2026
Last revised: November 5, 2026
For: a senior developer (React, .NET, Python) improving AI-assisted delivery and preparing for applied AI engineering roles in North America; local inference and model adaptation are optional breadth tracks.
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
| Q9 | What does the market actually ask for? | Sampled US/Canada roles emphasize production Python, LLM integration, retrieval, evaluations and reliable delivery. Azure/.NET fit your experience; specialist protocols, training and certificates are conditional. | 11 |

---

## 1. Plan at a glance

Recommended core order: **0 -> A -> B -> E -> D**. C and F are optional branches, not prerequisites for building an AI application. D can run alongside E once the manual coding workflow works.

You work full time: ~8 hours/week is a planning unit, not a cap or a promised completion schedule. Video durations exclude exercises; other study times below are estimates for selected sections. Do not repeat the completed §2 readings.

| Phase | Machine | When / scope | Goal | Exit criterion |
|---|---|---|---|---|
| 0. Safety and access | both + existing VPS | Before granting tools; requests in parallel | Learn trust boundaries, protect secrets, request approved work access; audit the running Hermes | Permissions checked; access questions recorded; Hermes secured before further VPS automation |
| A. Single-agent mastery | both | Core, first | Configure one harness per machine and learn its context, instructions and skills | One useful customization and a verified feature; §10 baseline for >=3 tasks |
| B. Coding-agent team | both | Core, after A | Manually coordinate implementer + fresh-context reviewer; add roles only when measured useful | Exact-commit handoffs, isolated review, failing CI blocks merge; initial comparison over >=5 matched tasks |
| E. Building AI applications | both; approved APIs | Core, after initial B | E1 concepts -> E2 native API -> E3 evals -> E4 framework/MCP -> E5 retrieval literacy -> E6 reliable delivery (§6) | One deployed application with evaluated behavior, authorized tools, traces, failure checks and a runbook |
| D. Scripted orchestration | home; work if approved | After B is repeatable; not an E prerequisite | Automate the proven coding workflow around existing harnesses (§5) | Bounded retries, deadlines, cancellation, persisted identity/state and explicit approval; Telegram optional |
| C. Local inference | home | Optional breadth (§7) | Understand quantization, memory and local API behavior through one small batch task | Measured quality, RAM and latency at two context sizes; no required local coding agent |
| F. Model adaptation / internals | home + cloud GPU | Optional, after readiness check (§8) | Study training and LoRA before a cloud fine-tune; pursue deeper theory only if useful | Held-out base/adapter comparison; serving/export comparison if supported; training from scratch is a separate specialization |

**Learning contract:** for each topic, complete the assigned conceptual study, explain the knowledge check, follow the selected SDK example, then apply it to a real task. A documentation link is not a substitute for the knowledge check. Meet required exit criteria before adding complexity; optional branches never block the core path.

**Currency means valid in 2026, not recently published.** Keep older evergreen concepts; mark historical tooling and deprecated sections explicitly. A recent date, working link, star count or course badge alone does not establish usefulness. Selected-source content was checked on Nov 5; gated labs, account permissions and runtime compatibility are not automatically verified.

**One continuing pet project:** a support-ticket and knowledge assistant, using synthetic/public documents at home: classify -> lookup -> draft -> evaluate -> add approved tools/retrieval -> deploy. Use work tickets only within company policy. Keep work code and credentials off the home PC and VPS. The full product idea can develop later.

**Access and cost:** documentation and free course videos form the default path. Course labs/certificates, model/judge APIs, Azure Search, hosting, storage and Actions can cost extra. Copilot, Claude Code and Google AI Pro access do not establish application-API or Azure entitlement. Check actual budgets, approved endpoints and Docker Desktop workplace licensing before practice; use local stubs while waiting, but do not mark live integration complete.

### 1.1 Phase 0 study before tool access

| Item / machine | Cost · selected study | Knowledge check -> practice |
|---|---|---|
| (work) [VS Code security](https://code.visualstudio.com/docs/agents/run/security) + [Copilot policy surfaces](https://docs.github.com/en/copilot/reference/supported-surfaces-for-policies) | Free docs · ~1 h | Explain trust, approvals, sandboxing and model-policy scope -> record permitted tools/models and demonstrate a denied mutation in a disposable session |
| (home) [Claude permissions](https://code.claude.com/docs/en/permissions) | Free docs · ~45 min | Explain prompt guidance versus enforced capability -> restrict tools before granting shell/network access; shell argument filtering is not OS isolation |
| (both) [OWASP prompt injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/), [sensitive disclosure](https://genai.owasp.org/llmrisk/llm022025-sensitive-information-disclosure/), [excessive agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/) | Free · ~1-2 h | Trace untrusted data and privileged actions -> use synthetic injection/secret tests; no production credentials in worker context |
| (existing VPS) [Hermes security](https://hermes-agent.nousresearch.com/docs/user-guide/security/) + [source updates/backups](https://hermes-agent.nousresearch.com/docs/getting-started/updating) | Free docs · ~1 h | Explain backend, credential and update boundaries -> audit §5.1, preserving local patches and checking the version actually running |

Send §3.6 access questions during Phase 0; pending CLI/cloud permissions do not prevent approved IDE study. Complete the VPS audit before expanding Hermes access, not before unrelated learning on either PC.

Last verified: Nov 5, 2026 (selected public content; no live security audit).

---

## 2. Core concepts (learn these words precisely)

| Term | Meaning | Practical consequence |
|---|---|---|
| Model | The LLM that produces text and tool calls | Picks quality, cost, latency, context size |
| Harness | The program around the model: file edits, shell, permissions, sessions (Claude Code, Codex, Copilot) | The same model behaves differently in different harnesses |
| Role / custom agent / subagent | A Markdown file with instructions, allowed tools and an optional model | Your "tester" is a config, not a new model |
| Subagent | A delegated worker; context may be fresh, forked or resumed depending on the harness | Choose fresh context explicitly for independent review; parent coordinates artifacts |
| Agent team | Several full sessions with a shared task list and messaging (Claude Code, experimental) | More parallelism, more coordination cost |
| Skill | A reusable procedure (SKILL.md + scripts) loaded on demand | "Add EF Core migration" written once, reused by every role |
| Hook | Shell command run on lifecycle events; can block an action | The way to enforce rules; prompts are only suggestions |
| MCP | Language-independent protocol to expose tools/data to compatible hosts | Learn concepts first; implement once and verify each host's features/version/authentication |
| A2A | Standard protocol for agents (possibly from different vendors) to call each other | Relevant when agents live in different services/orgs |
| Worktree | Extra working directory on its own branch, same repo | Isolates files; does NOT isolate ports, DBs, credentials |
| Eval | A repeatable test of model/agent behavior | The only way to know if a change helped |

Read first (2–3 h total, all free):
- [Building effective agents, Anthropic](https://www.anthropic.com/engineering/building-effective-agents) (Dec 2024; evergreen *concepts only*): workflows vs agents; the five base patterns (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer). Anthropic's own banner says "much of the tooling landscape described in this post has changed since December 2024", so skip its framework list and tooling advice. The patterns map onto the orchestrations in Agent Framework and the OpenAI Agents SDK (§3.2).
- [A practical guide to building AI agents, OpenAI (web version)](https://openai.com/business/guides-and-resources/a-practical-guide-to-building-ai-agents/): single-agent vs manager vs decentralized handoff designs, guardrails (2025, evergreen concepts; the old PDF's model names such as gpt-4o and o1 were dated, so check whether the web version updated them).
- [Effective context engineering for AI agents, Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) (Sep 2025; evergreen): what each worker should and shouldn't see.

Exit criterion: [notes/section-2-core-concepts.md](notes/section-2-core-concepts.md). It defines each term in your own words, maps the five patterns onto your current workflow, and lists what a reviewer should and shouldn't see. It also holds the **prompt-structure checklist** (background · instructions · tool guidance · output description) from the context-engineering article; use it for every role file and system prompt.

Last verified: Nov 5, 2026 for selected evergreen content; the OpenAI guide retains its Nov 2 check. Completed readings in PROGRESS.md remain credited; historical model names are not current setup recommendations.

---

## 3. How a team of coding agents works (Q1–Q4)

### 3.1 Roles

| Role | Input | Output | Tools allowed | Suggested model |
|---|---|---|---|---|
| Planner | Requirement, repo | Spec + task list + API contract | Read-only | Adequate reasoning quality, measured |
| Implementer (FE / BE) | One task + contract | Commit on own branch/worktree + handoff record | Edit, shell, tests | Strong coding model |
| Tester | Spec + contract (not the implementer's narrative) | New tests, coverage gaps, results | Edit tests only; isolated execution | Cheapest model that meets the test-quality bar |
| Reviewer | Spec + base/candidate diff + relevant code/tests/results | Findings with file/line + evidence | Read/search only; tests run separately | Fresh context; different model optional and measured |
| Orchestrator | Task list, results | Next step, integration, stop/escalate | Git, CI, launching workers | You first; then a script |

Start with **implementer + reviewer only**. Add the tester and planner when you can measure that they help.

### 3.2 How they communicate

There are only three mechanisms:

1. **Parent ↔ child (subagents).** The parent sends a bounded task and artifacts; the child returns a result. For independent review, choose a fresh context, not a conversation fork or a resumed implementation session. In Claude Code, named subagents can be messaged or resumed (`SendMessage`); nesting and worktree behavior depend on configuration. Its default worktree base can differ from the parent's `HEAD`, so record the actual base commit. Codex custom agents are configured separately in `.codex/agents/`.
   - [Claude Code subagents](https://code.claude.com/docs/en/sub-agents) · [Codex subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) · [Copilot custom agents](https://docs.github.com/en/copilot/concepts/customizations/custom-agents) (public preview in JetBrains, Eclipse, Xcode)
2. **Shared task list + messages (agent teams).** A lead creates tasks; teammates claim them, message each other, and hooks (`TaskCreated`, `TaskCompleted`, `TeammateIdle`; exit code 2 blocks) can reject work. Still experimental as of Nov 2026: enable it with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Teams need an interactive session (none under `-p` or the Agent SDK). Split panes don't work in Windows Terminal or the VS Code terminal, so use the default in-process mode on Windows. Build on subagents plus hooks first (`model:`, `isolation: worktree`), and add teams second. (Last verified: Nov 2, 2026.)
   - [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams)
3. **Artifacts in the repo.** Spec files, plan files, progress notes, commits, PRs, CI results. This works across any tools and models, and it's the most reliable mechanism.
   - [Effective harnesses for long-running agents, Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents): progress file + git history as memory between sessions.
   - Optional: [GitHub Spec Kit](https://github.com/github/spec-kit) provides specification/plan/task artifacts. Keep the lightweight §9 templates first; adopt another specification workflow only if it solves a real coordination problem. Stars and integration counts do not establish a hiring requirement or a universal standard.

In code frameworks the same ideas exist as named patterns: **sequential, concurrent, handoff, group chat, manager (Magentic)**.
- [Microsoft Agent Framework orchestrations](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations/): learn the patterns conceptually; confirm the selected package's feature status before implementation (§6.4).
- Optional, concept read: [OpenAI Agents SDK handoffs](https://openai.github.io/openai-agents-python/handoffs/) (`openai-agents` 0.24, still pre-1.0)
- [How Anthropic built its multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system) (Jun 2025; evergreen): real lessons on lead/worker delegation, token cost (~4× chat for one agent, ~15× for multi-agent, in Anthropic's 2025 system), and when multi-agent fails. Coding has fewer parallelizable subtasks than research.

### 3.3 How they hand off work

A handoff is valid only if it names **base and candidate commits** and says what was and wasn't verified. Use the templates in section 9. Rules:
- The reviewer gets requirements, exact diff, relevant surrounding code/tests and check results, **not** the implementer's persuasive narrative. Independence must not remove necessary evidence.
- If the candidate commit changes, earlier review/test results are void.
- After integration, run checks against the integrated revision too; passing a worker branch is not proof the combined change works.
- Each implementer owns specific files; two writers on one file = conflicts.
- Contract first: agree on request/response types and error cases before parallel FE/BE work.

### 3.4 How to orchestrate (three levels)

| Level | Who orchestrates | Use when | Learn from |
|---|---|---|---|
| 1. Manual | You run sessions and pass artifacts | Start Phase B here | Sections 3.3 and 9 |
| 2. Harness | Approved Copilot agents at work; Claude subagents at home; cloud/teams optional | Only after manual roles help; no calendar-based requirement to add agents | [Copilot custom agents](https://code.visualstudio.com/docs/agent-customization/custom-agents), [Claude subagents](https://code.claude.com/docs/en/sub-agents), [Agent teams](https://code.claude.com/docs/en/agent-teams) |
| 3. Code | Your script/service launches workers | Phase D: the manual flow already works repeatedly | [Claude Code headless](https://code.claude.com/docs/en/headless), [Codex non-interactive](https://learn.chatgpt.com/docs/non-interactive-mode), [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview) |

**Gates are code, not prompts:** tests, type checks, linters and repository-required checks. [Claude hooks](https://code.claude.com/docs/en/hooks) and [Copilot hooks](https://code.visualstudio.com/docs/agent-customization/hooks) can add local enforcement, but event names, payloads, errors and timeout behavior differ. A post-action hook cannot undo the action; do not assume every hook failure blocks work. Keep CI and repository protection authoritative.

### 3.5 Materials for phases A–B

Study the selected sections before their exercise. One main path per machine; optional courses are alternatives or depth, not a stack to complete.

| Item / machine / phase | Cost · selected study | What to learn -> practice output |
|---|---|---|
| (home, A) [Claude project memory](https://code.claude.com/docs/en/memory) + [skills](https://code.claude.com/docs/en/skills) | Free docs · ~1-2 h | Loading scope, concise instructions, context and on-demand procedures -> configure a real repo and demonstrate one recurring useful skill |
| (home, alternative A/B) [Claude Code in Action](https://academy.claude.com/courses/claude-code-in-action), [subagents course](https://academy.claude.com/courses/introduction-to-subagents), [skills course](https://academy.claude.com/courses/introduction-to-agent-skills) | Public course pages; verify enrollment access · main course 1 h video | Choose unfamiliar steering/customization/verification lessons; use current docs for exact behavior -> apply one improvement, not three compulsory courses |
| (work, A/B) [Copilot customization module](https://learn.microsoft.com/en-us/training/modules/configure-customize-github-copilot-visual-studio-code/) + [current agent reference](https://code.visualstudio.com/docs/agents/reference/custom-agent-file-reference) | Free · module 101 min including exercise | Instructions in A; roles/handoffs in B -> a repo-aware implementer and read/search-only reviewer. Current reference overrides stale fields/hook examples in the module |
| (both, A reference) [AGENTS.md](https://agents.md) | Free · ~20 min | Shared guidance versus harness-specific discovery -> verify the actual loaded instructions in a fresh session |
| (both, B) [Git worktree manual](https://git-scm.com/docs/git-worktree); home [Claude worktrees](https://code.claude.com/docs/en/worktrees) | Free · ~1 h; Git concepts evergreen | Explicit base commit, separate branches, shared refs/config -> isolate writers and allocate separate ports/DBs. Submodule support has limitations |
| (both, B) Actions [.NET](https://docs.github.com/en/actions/tutorials/build-and-test-code/net) and [Node.js](https://docs.github.com/en/actions/tutorials/build-and-test-code/nodejs) guides + [protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches) | Free docs; Actions/plan limits apply · ~2 h | Locked dependencies, tests, retained results, required checks and stale approvals -> seed a failing regression and show merge is blocked. Replace old runtime literals with supported repo versions |
| (home, optional B) [DLAI Claude Code course](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant), [Codex subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents), [Windows sandbox](https://learn.chatgpt.com/docs/windows/windows-sandbox) | Course access varies; docs free · select only missing topics | Parallel-session or second-harness depth -> one justified comparison, not another required setup |
| (work, conditional B) [Copilot cloud-agent module](https://learn.microsoft.com/en-us/training/modules/github-copilot-code-agent/); optional [Fundamentals path](https://learn.microsoft.com/en-us/training/paths/copilot/) | Free study; execution needs org access | Issue-to-PR and governance only if enabled -> one approved task; keep IDE-only handoffs as the fallback |

Knowledge check: explain instructions versus permissions, fresh versus inherited context, base/candidate commits, worktree resource limits, and tests versus review findings. Then complete A's >=3 baseline tasks and B's initial >=5 matched tasks (§10); these are starter observations, not statistically conclusive samples.

Windows note: use the native tools' documented supported setup. Claude team split panes require a supported tmux/iTerm2 environment; the default in-process team view avoids that requirement. Teams are optional.

**Shared instruction format for both machines:** use `AGENTS.md`. Copilot, Codex and Hermes read it. Claude Code reads it natively since v2.1.277, but only when there's no `CLAUDE.md`; set Project instructions to `claude-md-and-agents-md` to load both. Skill folders differ: Copilot reads `.github/skills`, `.claude/skills` or `.agents/skills`; Codex reads `.agents/skills`; Claude Code reads only `.claude/skills`. Copilot's cloud agent also reads a single root-level `CLAUDE.md` or `GEMINI.md`, so keep Anthropic-specific instructions out of work repos.

Last verified: Nov 5, 2026 (selected public content; exercises not executed).

### 3.6 Work-side team: Copilot only, no Anthropic models

Do not assume that one role file behaves identically in VS Code Local, the Copilot harness/CLI and the cloud agent. Start with the approved IDE surface you already have.

| Concept | Copilot equivalent |
|---|---|
| Role file | Use `.github/agents/<role>.agent.md` and the [current VS Code reference](https://code.visualstudio.com/docs/agents/reference/custom-agent-file-reference), [CLI reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference) or [cloud reference](https://docs.github.com/en/copilot/reference/custom-agents-configuration) for the actual runtime. `disable-model-invocation` controls delegation, not whether an agent calls a model; `user-invocable` controls picker visibility. Local handoffs carry context and are not independent review |
| Parallel subagents | CLI `/fleet` and `/tasks` are conditional on access and model policy. Each worker adds usage. Custom CLI subagents do not receive repo instructions by default; supply required context or use the documented `include-custom-instructions` behavior, checking editor conversion limits |
| Cloud implementer | Use the Copilot cloud agent only after its access and model-policy paths are cleared. Current work scope is GitHub Copilot only; separate Agent HQ Codex/Claude partner agents are out of scope |
| Model selection | Use only approved available models from the [live model list](https://docs.github.com/en/copilot/reference/ai-models/supported-models). Test another model/tier if useful; do not mandate a different vendor or assume automatic routing is compliant |
| PR review | Keep the existing Copilot PR review only within confirmed company policy. It is separate from your custom reviewer. Ask which providers can be used by review and background features; a model picker choice is not proof of their provider |
| Gates | GitHub Actions, branch protection, required checks |

**Copilot has several surfaces, each with its own admin policy.** Having Copilot doesn't mean you have all of them:

| Surface | What it is | How to check you have it |
|---|---|---|
| IDE chat / agent mode | Copilot inside VS Code or Visual Studio. Custom agents (`.github/agents`) work here | You're using it today |
| **Copilot CLI** | A separate terminal tool (`copilot`), GA since Feb 2026. This is where `/fleet` and headless scripting live | Install it per the [Copilot CLI docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli) and sign in. If the org policy is off, it says so |
| Cloud agent (formerly "coding agent") | Assign an issue to Copilot on github.com → it opens a PR | "Copilot" appears in an issue's Assignees list |
| Third-party coding agents (Agent HQ, public preview) | Separate Codex/Claude cloud agents; outside the current Copilot-only work scope | No work access check needed unless company scope changes |
| Copilot code review | Copilot as a PR reviewer | You're using it today |

**Your confirmed models (Nov 2026):** GPT-6.1 Sol/Terra, GPT-6 Sol/Luna and other recent OpenAI models; not GPT-6 Astra. Non-OpenAI availability remains unknown. OpenAI-only review is acceptable: use a fresh context, optionally another approved tier, and measure findings rather than equating vendor diversity with correctness.

**Work-account evidence (Nov 5, 2026):** learner-provided screenshots show Cloud as a selectable execution target and GPT-6.1 Sol, GPT-5.6 Sol and GPT-5.6 Luna in the model picker. This supplements the previously reported models, not a complete inventory. The learner also confirmed CLI installation/sign-in and availability of Actions, repository protection and workplace Docker use. Successful cloud execution, issue assignment and no-Anthropic policy coverage were reported tentatively ("I think so") and remain unconfirmed. Azure/API access, budgets and Docker licensing remain open.

**Phase 0 remaining access/admin questions:** verify Copilot cloud execution/issue assignment, permitted models and the scope of model policies; CLI installation/sign-in is confirmed. The learner confirmed that current work use is limited to GitHub Copilot, so separate Agent HQ partner agents are out of scope, not an access question to resolve. The CLI documents Anthropic defaults/fallbacks for some built-in agents; selecting OpenAI for the main session alone is insufficient. Verify delegated/review/permission-approval/background paths and [policy surfaces](https://docs.github.com/en/copilot/reference/supported-surfaces-for-policies), including [utility models](https://docs.github.com/en/copilot/concepts/models/utility-models). Editor schema acceptance does not prove runtime enforcement. Until a new surface is cleared, use the existing approved IDE workflow.

Last verified: Nov 5, 2026 (public references, work-account screenshots and learner-reported access; cloud execution and tenant policy enforcement remain unverified).

Practice (work, B): after the study/knowledge check in §3.5, run one real ticket through separate approved implementer and reviewer sessions, with deterministic tests and PR checks. Adopt CLI/subagents/cloud only if policy and measurements justify them.

Practice (home, B): specify one React list + .NET endpoint increment -> implement -> run tests separately -> give a fresh reviewer the exact base/candidate diff and evidence -> accept/reject findings -> verify integration. Codex via §4 is an optional additional perspective, not an access prerequisite. Add planner/tester roles or parallel worktrees only when needed.

---

## 4. Working with different models together (Q5)

**Home access (learner-reported, Nov 6, 2026):** Claude Pro with Claude Code is available; Codex access is not currently available. The learner is willing to add Codex if needed, but it remains optional and no purchase is assumed.

Study model selection and review trade-offs during B, then compare permitted configurations. C's local-model lab is optional. Different vendors may still share failure modes; independence is a hypothesis, not a correctness guarantee.

| Technique | What it is | How to do it |
|---|---|---|
| Home cross-model reviewer | Optional official [openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc) | Requires local Codex, Node 18.18+ and ChatGPT authentication (including Free) or API access. Follow its current installation/setup guide; use `/codex:review` on an exact diff. Stop-review gate is off by default and can loop/drain usage; leave it off initially |
| Per-role model choice | Cheapest adequate model/configuration per task, measured | Use the actual harness's model fields and inspect the resolved model/fallback. A cheaper tester or stronger reviewer is an experiment, not a universal rule; preserve independent acceptance criteria |
| Multi-vendor agents on GitHub | Claude and Codex as assignable agents on issues/PRs next to Copilot | GitHub Agent HQ (Pro, Pro+, Business, Enterprise; admin enables each partner agent). **Outside current work scope: GitHub Copilot only** (see 3.6) |
| Gemini as a home reviewer | Your Google AI Pro plan works with **Antigravity CLI (`agy`)** (announced on the Google Developers Blog, "transitioning Gemini CLI to Antigravity CLI"), not Gemini CLI. Google announced the move on May 19, 2026; Gemini CLI (and the free Code Assist for individuals tier) stopped serving AI Pro/Ultra on Jun 18, 2026. The binary is `agy` per the [Antigravity CLI docs](https://antigravity.google/docs/cli/headless/) | Use `agy` interactively, or hand it the exact diff manually, as an optional third reviewer. Wrapping or scripting `agy` on a consumer plan has unclear terms, so for scripted use take a paid Gemini API key (Gemini CLI still works with paid Gemini or Gemini Enterprise Agent Platform API keys) |
| Provider-neutral API layer | One interface where the application needs it | .NET: [IChatClient](https://learn.microsoft.com/en-us/dotnet/ai/ichatclient). Learn one direct API first (§6.2); Python routers such as [LiteLLM](https://docs.litellm.ai) or hosted [OpenRouter](https://openrouter.ai/docs) remain optional references, not extra required infrastructure. Pin dependencies, check advisories and never route work data through an unapproved provider |
| Model comparison by evidence | Same task, same acceptance criteria, different setups | [AI Coding Workflows: From Cloud to Local (DeepLearning.AI + JetBrains)](https://www.deeplearning.ai/courses/ai-coding-workflows-from-cloud-to-local) (home): free to enrol (certificate needs Pro), ~1.5 h, updated Sep 2026; swaps Claude Code ↔ OpenCode, routes via OpenRouter, ends with a local model in LM Studio (small quantized models only on your laptop) |

Two models agreeing doesn't prove the code is correct. Tests and your judgment decide.

Knowledge check -> practice: explain the variable being changed, resolved provider, context/effort settings and billing identity. Run isolated matched tasks from the same base commit without showing one run another's answers. Compare (a) fresh-context same-model review with (b) a different reviewer model, keeping implementation/checks fixed. Test per-role routing separately. A fixed cross-model reviewer is not a valid "one model for every role" control. Use §10 metrics and report uncertainty.

Last verified: Nov 5, 2026 (selected integration/reference content; no comparative experiment executed).

---

## 5. Scripted orchestration (Phase D)

Build a small coordinator **around existing harnesses** only after B's manual workflow proves useful. Study the execution/permission/state contracts below before scripting. This phase is not a gate to E; the Hermes trigger is an optional extension.

```text
task intake → validate scope → git worktree add → launch implementer (headless, JSON output)
→ collect result + commit SHA → run tests/CI → launch reviewer on that SHA
→ ≤2 repair rounds → stop for human approval
```

Your coordinator must handle: task IDs and state, exit codes, timeouts, cancellation, retry limits, commit identity (re-review after any change), cost/concurrency caps, secrets kept out of worker env, escalation to you.

| Resource / machine | Cost · selected study | Learn -> apply |
|---|---|---|
| (work, if approved) [Copilot programmatic execution](https://docs.github.com/en/copilot/how-tos/copilot-cli/automate-copilot-cli/run-cli-programmatically) + [CLI reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference) | Free docs; Copilot usage/CI costs · ~2 h | Scoped tools, explicit permitted models, JSONL and sessions -> implement one bounded `-p` invocation; do not copy blanket permissions or model-generated YES/NO shell gates |
| (home) [Claude headless](https://code.claude.com/docs/en/headless) | Free docs; plan/API usage · ~1-2 h | Structured output, permission prompts, resume, retry and termination behavior -> capture result, session and candidate identity with an explicit deadline |
| (home alternative) [Codex non-interactive](https://learn.chatgpt.com/docs/non-interactive-mode) | Free docs; selected authentication/usage costs | If choosing Codex workers, study JSON/schema output, sandboxing and resume -> substitute one worker without changing acceptance checks |
| (both) [Long-running harnesses](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) (evergreen concepts) + [Actions syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax) | Free reading; Actions costs · ~2 h | Checkpoints/artifacts, dependencies, permissions, outputs, concurrency and environments -> persist task/run/attempt/base/candidate state and recover one interrupted job |
| (both, .NET coordinator) [WaitForExitAsync](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.process.waitforexitasync) + [Process.Kill](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.process.kill) | Free docs · ~30 min | Cancellation of waiting is not worker termination; parent exit is not proof every descendant exited -> test deadline/cancel cleanup of owned processes |
| (home, optional depth) [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview), [OpenAI runtime options](https://developers.openai.com/api/docs/guides/agents), [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview) | Free reference; runtime/model usage extra | Alternative runtimes after the small coordinator works -> write a storage/permission/ownership comparison before replacing it; hosted/preview options are not required |

**Work route:** after §3.6 policy clearance, launch an approved Copilot worker with restricted tools; collect structured result and commit; run tests directly in CI; launch a fresh read/search-only reviewer; stop for human approval. Without CLI approval, continue the same artifact handoffs in the approved IDE. No Anthropic worker or personal VPS is part of this route.

Knowledge check -> practice: distinguish transient API retries from code repair, session IDs from task IDs, and checkpoints from exactly-once actions. Persist identity/state; enforce <=2 repair rounds, total deadlines and aggregate cost/concurrency limits externally. Inject a hanging worker, cancellation, bad result, duplicate intake and stale approval. Resume only a verified checkpoint; approval binds to the task and current candidate. A soft CLI usage limit is not a hard aggregate budget.

**Home trust/authentication:** a normal `claude -p` can load project hooks and MCP servers without interactive trust. Use reviewed configuration and isolation. `--bare` skips automatic instructions/hooks/skills/MCP discovery and subscription OAuth; it needs separate provider/API authentication and does not sandbox code. Check the current authentication/billing contract rather than assuming a plan funds every SDK, router or consumer-bot integration.

Last verified: Nov 5, 2026 (public contracts and study coverage; no unattended run executed).

### 5.1 Hermes on the existing VPS: safety now, coding trigger optional

Hermes is the Telegram front door and notification layer, not the code orchestrator. Your source-built instance runs on a Hetzner CX23 (2 vCPU, 4 GB RAM, 40 GB disk), not on the laptop. It cannot reach a powered-off or unreachable laptop. Use a cloud runner for laptop-off work; keep the small VPS for notifications and measured light jobs.

**Update status (learner-reported, Nov 6, 2026):** the learner reported completing an update and supplied `v0.22.0+119.g0e427d7` (2026-09-24), stable, local build. This shares the earlier reported base/date; latest-release status and the running gateway have not been independently checked. Backups, security and resource checks remain open in PROGRESS.md.

Study [security](https://hermes-agent.nousresearch.com/docs/user-guide/security/) and [source updating/backups](https://hermes-agent.nousresearch.com/docs/getting-started/updating) before changing this live installation. Explain the difference between updating a checkout, migrating data and restarting the service. Back up data and preserve source patches; inspect the actual install/channel and running gateway after an update. Check [releases](https://github.com/NousResearch/hermes-agent/releases) and [advisories](https://github.com/advisories?query=hermes-agent), not a frozen "minimum safe version". Some public advisories are unreviewed and do not identify a patched version; match the relevant fixes to the deployed commit.

**Phase 0 security checklist:**
- Restrict authorized users, for example `TELEGRAM_ALLOWED_USERS=<your numeric id>`; no allow-all flags or wildcard users.
- Review approvals and permanent allowlists; do not enable `off`/YOLO. Verify unattended denial behavior rather than assuming an unanswered prompt is safe.
- Use an appropriately isolated backend with restricted mounts, network and credentials. Docker skips dangerous-command approvals: it is not protection for secrets or host files deliberately exposed to the container.
- Audit `docker_forward_env`, skill-declared passthrough and mounted credential files, not only one config list. Code inside the container can read forwarded secrets.
- Set explicit CPU/RAM/disk limits below available VPS capacity; the documented 5 GB/50 GB example exceeds this machine. Measure host headroom with `free -m` and `docker stats` during a light test.
- Run as an unprivileged user, protect secret files, review installed hooks/plugins/MCP dependencies and use repo-scoped credentials.
- Treat Telegram/issue/PR content as untrusted. No work code or work credentials on the VPS.

| Optional home route | Study before enabling | Practice output |
|---|---|---|
| Cloud job, laptop can be off | §5 coordinator/Actions contracts; [Claude Code action](https://github.com/anthropics/claude-code-action) or [Codex action](https://github.com/openai/codex-action) if chosen | Trigger one scoped pet-project job and report the actual branch/PR/check result. Do not equate an action finishing with a PR being opened or accepted |
| Light worker on VPS | §5 headless/authentication docs plus the current Hermes backend/security contract | Run one small read-only task with limits and verify cancellation, permissions, memory and result delivery |
| Hosted scheduled task | [Claude scheduled-task documentation](https://code.claude.com/docs/en/routines), only if account access and current preview/API terms are confirmed | One explicit opt-in trigger after a supported route works; not a Phase D exit requirement |

Do not borrow a consumer plan token as Hermes's model-provider credential. A CLI supporting an authentication mode does not establish entitlement for every bot/router integration. Use the provider's documented permitted authentication, inspect credential precedence and billing, and keep uncertain consumer-plan automation under watch. Retain manual/local execution until the chosen route is verified.

Last verified: Nov 5, 2026 (maintenance/security content; actual deployment, trigger access and bot-auth entitlement remain unverified).

---

## 6. Building your own agents (Phase E)

Follow E1-E6 below. Each block gives conceptual study, a knowledge check and a project increment. Use one continuing project, not six demos. Begin with .NET or Python, not both; the small Python port in E6 supplies additional career evidence without requiring a second framework. Security basics were already assigned in Phase 0.

### 6.1 E1: Agent concepts and tool design

| Item / machine | Cost · study | Selected knowledge -> output |
|---|---|---|
| (both concepts; home hosted labs) [Agentic AI, Andrew Ng](https://www.deeplearning.ai/courses/agentic-ai/) | Free videos; Pro labs/graded work · 9 h 55 min full course; ~6-8 h selected estimate | Modules 1-4: workflows, reflection, tool use, evaluation/error analysis. Defer highly autonomous multi-agent patterns -> specify a bounded user workflow and its simpler baseline |
| (both concepts) [Writing tools for agents](https://www.anthropic.com/engineering/writing-tools-for-agents) (evergreen selected concepts) | Free · ~1 h with notes | Naming/contracts, targeted results, pagination, actionable errors and evaluation -> design one allowlisted lookup with typed inputs, bounded output and explicit failure cases |

Knowledge check: when would ordinary code or one model call suffice? Explain how tools are chosen and executed, what may change state, where approval belongs and how success will be measured. Historical model names and vendor defaults in articles are not setup instructions.

Then apply: write the ticket-assistant's acceptance criteria, non-AI baseline, trust boundaries and initial labeled examples. Do not choose multi-agent architecture merely to satisfy the exercise.

Last verified: Nov 5, 2026 (public course/module content; paid labs not executed).

### 6.2 E2: Native APIs before frameworks

Choose one implementation first. Work uses approved Azure endpoints and non-Anthropic models. Home requires separately available API access; existing consumer subscriptions do not provide it automatically. Use a supported runtime, not an old tutorial's literal version.

| Item / machine | Cost · selected study | Selected knowledge -> output |
|---|---|---|
| (work/.NET; home if using Azure) [Azure Responses](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/responses) + [structured outputs](https://learn.microsoft.com/en-us/azure/foundry/openai/how-to/structured-outputs) | Free docs; Azure inference metered · ~2-3 h | Response lifecycle, schemas, refusal/filter/incomplete handling, function-call continuation -> typed ticket extraction and one lookup without an orchestration framework |
| (both, .NET) [IChatClient](https://learn.microsoft.com/en-us/dotnet/ai/ichatclient) | Free docs/library; chosen provider usage · ~1 h | Tool invocation, conversation state and telemetry; skip experimental reduction/routing initially -> integrate the same contract with the approved provider |
| (home Python, or approved Azure-compatible adaptation) [OpenAI Python SDK](https://github.com/openai/openai-python), [structured outputs](https://developers.openai.com/api/docs/guides/structured-outputs.md), [function calling](https://developers.openai.com/api/docs/guides/function-calling.md), [conversation state](https://developers.openai.com/api/docs/guides/conversation-state.md) | Free docs; API metered · ~2-3 h | Parse/validate, zero/one/multiple tool calls, call IDs, retained output items, history, retries/timeouts -> implement the same bounded behavior; do not copy public OpenAI credentials/endpoints into work |
| (home, optional specialization) [Building with the Claude API](https://academy.claude.com/courses/building-with-the-claude-api) | Free course content; Anthropic API extra · 9 h full course | Select unfamiliar context, tool-use and prompt-evaluation sections -> implement one feature only if choosing Anthropic at home; skip duplicate MCP/RAG study |

Knowledge check: schema validity is not semantic correctness or authorization. Explain who executes a tool, how outputs re-enter the conversation, how refusals differ from success, and how context/round/timeout limits stop the loop. Responses and Chat Completions use different output-format fields; Azure and public OpenAI schema/parallel-tool support differ. Use the selected endpoint's documented subset.

Then apply: classify/extract -> read-only lookup -> draft. Test malformed/invalid arguments, unknown tools, refusals, incomplete/filtered output and provider failure. Enforce a chosen small round limit, deadline and usage budget in code; preserve an evidence-based baseline. No autonomous external mutation yet.

Last verified: Nov 5, 2026 (selected API contracts; SDK/model combinations require a practice-time smoke test).

### 6.3 E3: Evaluation and observability from the first feature

| Item / machine | Cost · study | Selected knowledge -> output |
|---|---|---|
| (both concepts; home vendor labs) [Evaluating AI Agents](https://www.deeplearning.ai/short-courses/evaluating-ai-agents/) | Videos/beta access as offered; graded work/labs depend on account · 2 h 41 min full course | Tracing, component/trajectory evaluations and experiments -> explain one observed failure before creating a judge |
| (both) [AI Evals FAQ, Hamel Husain & Shreya Shankar](https://hamel.dev/blog/posts/evals-faq/) | Free · ~2-3 h selected | Sampling/error analysis, train/dev/test separation, human-calibrated judges and CI versus online monitoring -> a failure taxonomy, data-split rationale and release rubric; practitioner defaults are not universal rules |
| (both, .NET) [Evaluation reporting](https://learn.microsoft.com/en-us/dotnet/ai/evaluation/evaluate-with-reporting) | Free docs/libraries; judge calls extra · ~1-2 h | Scenario/execution identity, assertions, custom evaluators, caching and reports -> seed a regression that fails B's CI and retains a report |
| (both) [OTel GenAI conventions](https://github.com/open-telemetry/semantic-conventions-genai/blob/main/docs/gen-ai/client-inference.md) + [workflow observability](https://learn.microsoft.com/en-us/agent-framework/workflows/observability) | Free · ~1 h selected | Traces/spans/metrics, context propagation, latency/tokens and sensitive capture -> instrument one request/tool flow. Semconv is **Development**; check supported attributes rather than memorizing mutable names |
| (both, local default) [Standalone Aspire](https://aspire.dev/dashboard/standalone/) + [GenAI visualization](https://aspire.dev/dashboard/explore/#genai-telemetry-visualization) | Free local viewer; Docker requirements apply · ~1 h | Instrumentation versus viewing, export, redaction and short-term diagnostics -> inspect the request. Bind appropriately; UI authentication alone does not secure OTLP ingestion |
| (home or approved work, optional alternative) [Langfuse](https://langfuse.com/docs) or LangSmith | Docs free; hosting/usage extra | Dataset/trace/eval workflow -> compare one concrete need with the default, not deploy several viewers |
| (both, optional paid depth) [AI Evals for Engineers & PMs](https://maven.com/parlance-labs/evals) | Listed $4,400 USD; check cohort/workload before purchase | Deeper error analysis, evaluator validation and production practice -> improve an existing evaluation process. Optional because of scope/cost, not invalid because it overlaps |

Knowledge check: deterministic checks versus model judges; representative cases versus an untouched holdout; judge error/agreement; quality versus token-derived cost and latency. A repeatedly used CI suite is not the final untouched test set. Cached reports can hide provider drift; unsupported model parameters in sample code must be removed for the chosen deployment.

Then apply: start with roughly 30 varied smoke/regression cases, expand by failure category and risk, preserve a separate holdout, calibrate any judge against human labels, and demonstrate a failing release gate. Record quality, human corrections, tokens/cost and p95 latency separately. Sensitive-content capture stays off unless explicitly approved; use uncached runs to check live provider changes. Thirty cases alone do not prove representativeness or production safety. Retain production telemetry through E6, not only the development dashboard.

Last verified: Nov 5, 2026 (public material; gated lessons, judge reliability and viewer rendering require hands-on verification).

### 6.4 E4: One framework, general MCP, then C# implementation

**MCP is the protocol; the C# SDK is one implementation.** First understand general MCP, then use .NET because you already know it. No Python SDK course is required to build the .NET server. A tool-only MCP server does not inherently need its own LLM subscription.

| Item / machine | Cost · selected study | Selected knowledge -> output |
|---|---|---|
| (both, .NET default) [Agent Framework overview](https://learn.microsoft.com/en-us/agent-framework/overview/) + [workflow construction](https://learn.microsoft.com/en-us/agent-framework/concepts/workflows/builder-and-execution) | Free; model usage extra · ~2-3 h | Agent versus deterministic workflow, typed executors, state ownership and sequencing -> port the already-working E2 behavior, not invent new complexity |
| (both) [Tool approval](https://learn.microsoft.com/en-us/agent-framework/agents/tools/tool-approval) + [checkpoints](https://learn.microsoft.com/en-us/agent-framework/workflows/checkpoints) | Free docs · ~1-2 h | Pending-request/session binding, durable versus in-memory state, executor identity and replay -> explain denial and restart recovery before coding them |
| (home course; both conceptual reading) [Introduction to MCP](https://academy.claude.com/courses/introduction-to-model-context-protocol) | Free content · 1 h video | Host/client/server and tools/resources/prompts -> diagram a tool call. Python SDK v1 walkthroughs need version-matched treatment; Anthropic chat exercises are home-only |
| (both, required current concepts) MCP [introduction](https://modelcontextprotocol.io/docs/getting-started/intro), [architecture](https://modelcontextprotocol.io/docs/learn/architecture), [server concepts](https://modelcontextprotocol.io/docs/learn/server-concepts) + [July revision changes](https://modelcontextprotocol.io/specification/2026-07-28/changelog) | Free · ~1-2 h selected | Protocol contracts, transports, discovery and host policy -> explain what transfers across Python/C#/Copilot and which features need compatibility checks |
| (both, .NET implementation) [C# SDK v2.3.1 getting started](https://github.com/modelcontextprotocol/csharp-sdk/blob/v2.3.1/docs/concepts/getting-started.md); home [Claude MCP configuration](https://code.claude.com/docs/en/mcp) if chosen | Free docs/SDK · ~2-3 h | Reviewed local stdio server first, tool schema/discovery/calls; HTTP later -> expose two read-only API tools and test with Inspector or a compatible approved client. Pin packages to match the guide |
| (both concepts; home external-vendor labs) [HF MCP Course](https://huggingface.co/learn/mcp-course/en/unit0/introduction) | Free · selected units only | Alternative general introduction, not a second mandatory course -> explain the same protocol and adapt one example. Use current SDK/protocol sources for implementation; the lifecycle/sandbox corrections below are required |
| (home, optional legacy/advanced depth) [MCP Advanced Topics](https://academy.claude.com/courses/model-context-protocol-advanced-topics) | Free content · 1.5 h full course | Transport, JSON-RPC, progress and scaling concepts -> compare one legacy design with the current spec; do not use deprecated features as new-design requirements |
| (work Azure, optional guided alternative) [Develop AI agents on Azure](https://learn.microsoft.com/en-us/training/paths/develop-ai-agents-azure/); [.NET AI lessons](https://github.com/microsoft/generative-ai-for-beginners-dotnet) | Free reading; Azure labs metered | Select unfamiliar AI integration lessons, skip programming basics -> substitute one guided exercise for a corresponding implementation, not another full curriculum |

The Advanced Topics author notes: "This course teaches MCP as it worked before the 2026-07-28 revision of the specification." Roots, Sampling and Logging are **deprecated, not removed**; old initialization/session behavior is legacy. Use current stdio or Streamable HTTP guidance; legacy HTTP+SSE deprecation does not mean every SSE stream is deprecated. SDK v2.3.1 was observed on Nov 5 and includes a security fix; verify the selected host's actual supported protocol/features. Avoid blindly executing the quickstart's unversioned `npx` package; begin with reviewed local code and a minimized worker environment.

For this versioned C# guide, install with `dotnet add package ModelContextProtocol --version 2.3.1`; pin the matching HTTP package too if selected. At practice time, check patched releases and update the guide/package selection together. The HF course's `shutdown`/`exit` message diagram is not MCP protocol: even the [older lifecycle specification](https://modelcontextprotocol.io/specification/2025-11-25/basic/lifecycle) defines no specific shutdown messages. Stdio does not create a sandbox; [local-server security guidance](https://modelcontextprotocol.io/specification/2026-07-28/basic/security_best_practices#local-mcp-server-compromise) requires separately supplied isolation because local servers run with client privileges.

MAF core and integration feature status differ. The [orchestration release announcement](https://devblogs.microsoft.com/agent-framework/agent-frameworks-orchestration-patterns-reach-1-0/) describes stable patterns, while the [Magentic tutorial](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations/magentic) still carries experimental .NET markers. Treat that conflict as unresolved for a selected package; advanced Magentic/preview integrations are not required. Use one simple workflow and pin its dependencies.

Knowledge check: tools versus resources versus prompts; protocol versus host permissions; stdio versus remote HTTP; state isolation, actual approval and durable checkpoint limitations. An approval demo that returns `true` automatically is not human consent; checkpoints do not guarantee exactly-once external actions.

Then apply: connect the two read-only .NET tools to an approved host, validate errors/cancellation and record compatibility. Add a draft -> human decision -> stub action workflow; denial/expiry does nothing. Recover from process restart using a durable store and prevent duplicate effects. Do not load untrusted checkpoint data or assume the filesystem store is a concurrent production database.

Last verified: Nov 5, 2026 (selected concepts/SDK content; interoperability and runtime recovery not executed).

### 6.5 E5: Retrieval fundamentals, authorization and document lifecycle

Retrieval literacy is core career knowledge. A production RAG feature is conditional: add it only if external/private/changing information improves the measured task. RAG does not eliminate hallucinations or prompt injection, and not every retrieval system needs vectors.

| Item / machine | Cost · selected study | Selected knowledge -> output |
|---|---|---|
| (home Python course; both concepts) [Retrieval Augmented Generation](https://www.deeplearning.ai/courses/retrieval-augmented-generation-rag/) | Free videos; labs depend on account · 26 h 03 min full course; ~8-12 h selected estimate | Overview if needed, modules 2/3/5: search, embeddings/chunking, vector databases, evaluation and production trade-offs -> explain the retrieval/generation boundary before implementation; revisit generation/context material if missing |
| (work Azure; home approved account or conceptual study) [Hybrid search](https://learn.microsoft.com/en-us/azure/search/hybrid-search-overview) + [semantic ranking](https://learn.microsoft.com/en-us/azure/search/semantic-search-overview) | Free docs; search/embedding/ranker usage extra · ~1 h | BM25/vector candidates, RRF fusion and subsequent reranking -> design a comparison rather than assuming hybrid always wins |
| (both concepts; work identity integration) [Access-token validation](https://learn.microsoft.com/en-us/entra/identity-platform/access-tokens) + [security trimming](https://learn.microsoft.com/en-us/azure/search/search-security-trimming-for-azure-search) | Free docs; approved tenant/resources needed · ~1-2 h | Trusted backend identity, tenant boundaries and mandatory filters -> define who may retrieve each document; use validation middleware, not hand-written JWT parsing |
| (work Azure; synthetic home equivalent) [Changed/deleted documents](https://learn.microsoft.com/en-us/azure/search/search-how-to-index-azure-blob-changed-deleted) + [index projections](https://learn.microsoft.com/en-us/azure/search/search-how-to-define-index-projections) | Free docs; storage/search/embedding usage extra · ~1-2 h | Incremental ingestion, deletion policy, parent/chunk lifecycle and explicit metadata mapping -> define freshness and source/version provenance before indexing |

Knowledge check: evaluate retrieval separately from grounded answers; explain citations and abstention, permission propagation, revocation lag, stale chunks and cache isolation. Principal strings are not authentication. Application-managed filters require a trusted backend; [native ACL enforcement](https://learn.microsoft.com/en-us/azure/search/search-document-level-access-overview) has preview constraints. A reranker cannot retrieve a document absent from its candidate set.

Then apply: use synthetic documents and query/relevance labels; compare keyword, dense, hybrid and optional reranking with Recall@k/ranking quality, groundedness, latency and cost. Test two users' permissions, forged identity inputs and revocation; unauthorized text must not reach the model, citations, shared caches or traces. Add/edit/shorten/delete/restore content and verify freshness, provenance, no orphan chunks and idempotent reruns. Azure deletion policies must be configured before the first run; soft-delete/versioning/retention limitations and projection-versus-parsing differences must be checked, not assumed away.

Last verified: Nov 5, 2026 (public syllabus/docs; hosted course labs and end-to-end authorization not executed).

### 6.6 E6: Reliable application delivery

Study each selected topic before its corresponding increment. Use existing engineering knowledge; do not add beginner React/.NET/Python courses. B supplies CI foundations, E2 API contracts, E3 evaluations, E4 state/approvals and E5 any retrieval lifecycle.

| Item / machine | Cost · selected study | Selected knowledge -> output |
|---|---|---|
| (both) [.NET HTTP resilience](https://learn.microsoft.com/en-us/dotnet/core/resilience/http-resilience), [API implementation guidance](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-implementation), Polly [timeout](https://www.pollydocs.org/strategies/timeout.html) / [fallback](https://www.pollydocs.org/strategies/fallback.html) | Free · ~2-3 h | Retry/backoff/jitter, Retry-After, total/attempt deadlines, cancellation, idempotency, throttling and explicit degradation -> fault-injection plan with no duplicate actions or false success |
| (both, small Python component) [uv projects](https://docs.astral.sh/uv/guides/projects/), [uv CI](https://docs.astral.sh/uv/guides/integration/github/), [pytest practices](https://docs.pytest.org/en/stable/explanation/goodpractices.html) | Free tools/docs; CI allowances apply · ~2-3 h | Locked environments, package layout, fixtures/parametrized checks and installed-artifact testing -> port one bounded worker/contract, not the whole .NET app or a second framework |
| (both, local first) [Docker .NET containerization](https://docs.docker.com/guides/dotnet/containerize/) | Free docs; check workplace Desktop licensing · ~1-2 h | Multistage build, non-root runtime, configuration and ports -> run the existing application locally from an identifiable image |
| (approved work Azure or own home account) [Container Apps source deployment](https://learn.microsoft.com/en-us/azure/container-apps/quickstart-code-to-cloud) | Free docs; subscription/resources billed · ~1 h | Build/push/deploy and verify your own app -> one small working hosted endpoint, not merely a supplied sample image |
| (Azure) [Managed identity](https://learn.microsoft.com/en-us/azure/container-apps/managed-identity) + [secret references](https://learn.microsoft.com/en-us/azure/container-apps/manage-secrets) | Free docs; service usage extra · ~1-2 h | Runtime identity versus deployment identity, scoped roles and rotation -> no embedded production secrets and a verified synthetic-secret rotation |
| (Azure) [Health probes](https://learn.microsoft.com/en-us/azure/container-apps/health-probes), [revisions](https://learn.microsoft.com/en-us/azure/container-apps/revisions-manage), [GitHub deployment](https://learn.microsoft.com/en-us/azure/container-apps/github-actions), [current Azure Login/OIDC](https://github.com/Azure/login) | Free docs; Actions/cloud usage extra · ~2 h | Startup/readiness/liveness, immutable releases, scoped federated deployment and rollback -> eval-gated release, smoke check and failed-release recovery |
| (Azure production) [OpenTelemetry export to Application Insights](https://learn.microsoft.com/en-us/azure/azure-monitor/app/opentelemetry-enable) | Free docs; ingestion/retention billed · ~1 h | Instrumentation/export, redaction, sampling and retention -> retained traces plus a runbook for observing quality/cost/latency and diagnosing failures |
| (both risk reading; home vendor exercises) [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/); optional [Red Teaming LLM Applications](https://www.deeplearning.ai/short-courses/red-teaming-llm-applications/) methods | Free HTML risk reading; course/labs depend on account · ~1-2 h selected | Trust boundaries, unsafe outputs, disclosure and excessive agency -> adversarial negative tests. Reading a risk list does not replace practical testing; course's gated Important Note/lab compatibility remains unverified |

Knowledge check: SDK plus HTTP retries must not multiply unchecked; standard retry handlers can retry writes, so explicitly justify/disable unsafe retries. Cooperative timeout/cancellation does not terminate arbitrary work. Fallback must preserve authorization and visibly report degraded/failure behavior. Understand probes, rollout, rollback and identity before deployment. The deployment tutorial's old `azure/login@v1` example is EOL: use the maintainer's supported v3/OIDC guidance. Current new-app telemetry guidance uses the Microsoft OTel distro; do not initialize it and the older Azure Monitor distro together.

Then apply: inject 429/503 responses, hangs, cancellation and repeated submissions; demonstrate bounded work and no duplicate side effects. Run the Python component's tests from a clean locked checkout and smoke-test its built package. Release the app through tests/evals, health and smoke checks; recover a deliberately bad revision. Revision rollback does not restore shared secrets or database state. Retain a runbook covering access, deploy, verify, promote, observe, rollback and cleanup. Local Docker practice completes local learning, not Azure identity or live-release exit criteria.

**Phase E capstone:** one useful deployed workflow, a bounded native API loop, authorized tools and .NET MCP integration, calibrated regression/held-out evaluations, traces, failure/security checks and an operational runbook; a small Python component demonstrates transfer. Keep retrieval and additional agents only where they improve the baseline. Use synthetic/permitted data for public portfolio evidence.

Watch, not a validated required reading: [OWASP Agentic Applications 2026 report](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/). Its landing page was checked, but the report PDF could not be extracted in this pass. Use the verified HTML risks and harness security material meanwhile; no claim that this pass reviewed its full contents.

Last verified: Nov 5, 2026 (selected public content; cloud/account/lab execution and total cost remain unverified).

### 6.7 Conditional depth: Python frameworks and A2A

| Item / machine | Cost · selected study | Learn -> apply only if needed |
|---|---|---|
| (home first; approved provider at work) [LangGraph quickstart](https://docs.langchain.com/oss/python/langgraph/quickstart.md), [checkpointers](https://docs.langchain.com/oss/python/langgraph/checkpointers.md), [interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts.md) | Free docs; provider/storage usage extra · ~2-3 h | Stateful Python execution, persistent checkpoints and approval -> one process-restart/resume test with idempotent effects. Do not copy Anthropic-backed examples into work |
| (optional course alternative) [LangGraph Essentials](https://academy.langchain.com/courses/langgraph-essentials-python) | Check enrollment; select unfamiliar lessons | Guided introduction if preferable to the quickstart -> the same state/approval exercise, not another compulsory course |
| (home or approved work, alternative only) [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) | Free docs; model usage extra | A lighter Python approach if it fits the task -> compare requirements before choosing one Python framework; do not stack frameworks |
| (both concepts; only for separate services) [A2A specification](https://a2a-protocol.org/latest/specification/) | Free · ~1 h overview | Agent Cards, tasks/artifacts, authentication and relation to MCP -> document a real interoperability need before building a remote agent peer |

LangGraph remains useful for a stateful Python service, not a coding-team prerequisite or a default required alongside MAF. [Thoughtworks' April 2026 assessment](https://www.thoughtworks.com/radar/languages-and-frameworks/langgraph) is explicitly not on the current edition; treat it as historical architectural judgment, not a fresh November verdict. Downloads are not hiring evidence. Persistent state is required for crash recovery; resuming can rerun code, so checkpointing does not remove idempotency requirements. Skip beta/custom persistence features until needed. A2A examples/prose span version differences and contain blocking-behavior ambiguity; check the chosen version/SDK's normative contract before implementation.

Last verified: Nov 5, 2026 (selected protocol/runtime concepts; no interoperability run).

---

## 7. Running models locally (Q6)

**Optional Phase C, home PC.** This is a local-inference learning lab, not a prerequisite for E or D. Model-routing comparisons belong to §4. Study the memory/API concepts below, explain the knowledge check, then run one bounded batch experiment. Phase F may later reuse this serving path.

### 7.1 What to learn

| Topic | Why it matters |
|---|---|
| Quantization (GGUF Q4/Q5/Q8) | Decides whether a model fits your RAM/VRAM; [GGUF on Hugging Face](https://huggingface.co/docs/hub/gguf) |
| Runtime memory | Weight-file size is not total RAM: include KV cache, context, concurrency, runtime buffers, Windows and shared iGPU memory. For MoE, all required expert weights still affect storage/memory even when fewer parameters are active |
| Latency and throughput | Distinguish cold load, prompt processing and generation. Measure the actual task; no tokens/second or model-class fit is guaranteed on this laptop |
| Context length | Larger context/concurrency can exhaust RAM before weights do. Start small and compare two explicit context settings; a model's advertised maximum is not a suitable laptop default |
| Tool-calling support | Not every open model calls tools reliably; agents need it |
| Licensing | Open-weight ≠ unrestricted commercial use; read the model license |

### 7.2 Tools

| Item / scope | Cost · selected study | Learn -> apply |
|---|---|---|
| [Ollama FAQ](https://docs.ollama.com/faq.md), [GGUF import](https://docs.ollama.com/import.md), [generation API](https://docs.ollama.com/api/generate.md) | Free local tooling/docs; model license separate · ~1-2 h | Default lab: context/memory controls, quantized import, timing/token counters and API output -> one small local batch job with recorded measurements |
| [Hardware support](https://docs.ollama.com/gpu.md) | Free reference | CPU first. Vulkan availability and the iGPU opt-in do not certify Iris Xe speed/stability -> test acceleration only if useful and keep CPU if better |
| [LM Studio](https://lmstudio.ai/docs/app), [llama.cpp](https://github.com/ggml-org/llama.cpp), [OpenVINO](https://docs.openvino.ai) | Optional reference; do not install all three | Alternative GUI or runtime control -> compare one concrete missing capability only after the default lab |
| [vLLM](https://docs.vllm.ai) | Optional GPU-server reference; rented compute extra | Throughput/concurrency specialization -> one later rented-GPU benchmark if needed, not a native-Windows laptop requirement |

Start with one compatible 1-4B-class quantized model, for example [Qwen3.5 4B](https://ollama.com/library/qwen3.5). Verify its actual file, license and context settings. Larger alternatives such as the previously listed 9B/Gemma profiles are conditional on measured runtime headroom and latency, not automatically "the best that fits".

**Hardware:** do not buy a GPU to complete this roadmap. Price learning compute per experiment and set a cap; no annual budget is guaranteed. Consider a RAM upgrade only after measuring a task that benefits and confirming the laptop's actual slot/layout constraints. GPU shopping prices are not required learning material.

### 7.3 Local models inside your coding-agent team

- **Optional Claude Code compatibility experiment (home):** follow [Ollama Anthropic compatibility](https://docs.ollama.com/api/anthropic-compatibility) only after the direct API lab. Provider-compatible endpoints do not implement every hosted feature. Check resolved settings and benchmark prompt processing/tool reliability; do not assume any fixed parameter count makes an interactive coding agent practical here.
- **Codex / OpenAI-compatible tools:** point them at Ollama/LM Studio's OpenAI-compatible endpoint.
- **.NET apps:** `IChatClient` from Microsoft.Extensions.AI with the Ollama provider, so a config switch swaps local and cloud.
- **Realistic first use:** a short summarization/classification batch or test-stub draft, with public/synthetic data and an explicit quality check. Hosted models remain the default for interactive coding until an alternative passes your measurements.
- Local inference ≠ private by default: plugins, telemetry and MCP servers can still send data out.
- A CPU-only VPS isn't faster than your laptop; its only advantage is being always on. Use it at most for tiny always-on helpers for Hermes (classification, short summaries). Don't rent a VPS for inference.

Knowledge check: explain weights versus KV cache/runtime memory, quantization versus training, prefill versus decoding and why local does not automatically mean private.

Then apply (home): run one model on the same small labeled batch at two context sizes. Record quality, peak RAM, cold/warm latency, prompt-processing and generation throughput. Keep inputs/output limits fixed and report failures. If later adding it to a coding workflow, test that one change separately against the §4 control; local inference is not a required reviewer or team role.

Last verified: Nov 5, 2026 (selected runtime/API content; actual model fit, acceleration and performance unverified until measured).

---

## 8. Building and adapting models (Q7)

**Optional Phase F, home plus cloud GPU. Study before training.** Senior application-development experience does not establish PyTorch or training readiness. Use the bridge only for missing knowledge; full Karpathy, a full ML degree and CS336 are not prerequisites for a first LoRA experiment.

### 8.1 Readiness bridge, only where needed

| Material / home | Cost · selected study | Knowledge check -> small CPU exercise |
|---|---|---|
| [Zero to Hero](https://karpathy.ai/zero-to-hero.html), first micrograd lesson; corrected author notebooks [first half](https://raw.githubusercontent.com/karpathy/nn-zero-to-hero/master/lectures/micrograd/micrograd_lecture_first_half_roughly.ipynb) / [second half](https://raw.githubusercontent.com/karpathy/nn-zero-to-hero/master/lectures/micrograd/micrograd_lecture_second_half_roughly.ipynb) | Free; evergreen concepts · select gradients/graph/loss sections | Explain chain rule, accumulated gradients and gradient descent -> trace one gradient. The author corrects a video's `=` to `+=`; use the corrected notebook, not the old typo |
| [PyTorch Basics](https://docs.pytorch.org/tutorials/beginner/basics/intro.html): Tensors, DataLoaders, Autograd, Optimization | Free · ~2-4 h selected estimate | Shapes/matrix operations, minibatches, `forward`, loss, backward/update and evaluation -> train a tiny CPU example; distinguish `eval()` from `no_grad()` |
| HF [causal LM / transfer](https://huggingface.co/learn/llm-course/chapter1/4), [tokenizers](https://huggingface.co/learn/llm-course/chapter2/4), [training/evaluation](https://huggingface.co/learn/llm-course/chapter3/3), [validation sets](https://huggingface.co/learn/llm-course/chapter5/3#creating-a-validation-set) | Free · ~1-2 h selected estimate | Next-token prediction, IDs versus representations, loss versus task metrics and uncontaminated train/dev/test partitions -> explain an overfitting/leakage case. The BERT classification example is not a causal-LM LoRA recipe |

After the reading, use a small dataset subset to show weights change during training but not validation; preserve untouched test data. Explain padding/truncation, causal labels/masking, learning rate, batch/epoch and frozen-base memory before model-sized training. Random row splitting alone does not prevent document/conversation/temporal leakage. This bridge does not establish the advanced mathematics/systems prerequisites for CS336.

### 8.2 Adaptation first; deeper specialization optional

| Step / machine | Resource | Cost · selected study | Learn -> output |
|---|---|---|---|
| 1. Post-training concepts / home | [Post-training of LLMs](https://www.deeplearning.ai/short-courses/post-training-of-llms/) + [PEFT LoRA](https://huggingface.co/docs/peft/developer_guides/lora) | Free videos/docs; course access varies · 1 h 26 min video plus ~1 h selected docs | SFT versus preference/RL methods; frozen weights, rank/targets/scaling, adapters versus quantization -> justify LoRA versus prompting/retrieval for one task; skip advanced adapter variants initially |
| 2. Data and training / home + cloud GPU | Unsloth [datasets](https://unsloth.ai/docs/get-started/fine-tuning-llms-guide/datasets-guide.md), [notebooks](https://unsloth.ai/docs/get-started/unsloth-notebooks.md), [evaluation/early stopping](https://unsloth.ai/docs/basics/troubleshooting-and-faqs.md); [TRL](https://huggingface.co/docs/trl) as reference | Free material; GPU time extra · ~2-3 h selected reading, practice separately | Data rights/quality, templates, trainable parameters, compatible environment and validation -> one bounded supported-model LoRA run with versioned data/config and frozen splits |
| 3. Export and serving if supported / cloud then home | [Unsloth GGUF export](https://unsloth.ai/docs/basics/inference-and-deployment/saving-to-gguf.md) + §7 import/API material | Free docs; conversion/inference resources extra · ~1 h study | Merge/export memory, supported model format, tokenizer/template/EOS consistency -> save/reload the adapter; if export is supported, reevaluate that artifact too. Use cloud for CUDA/Linux recipes, not the Windows CPU unchanged |
| Optional theory / home + cloud when needed | [Zero to Hero](https://karpathy.ai/zero-to-hero.html) beyond micrograd; selected [HF LLM Course](https://huggingface.co/learn/llm-course/chapter1/1) | Free; coding time varies | Autoregression, attention, tokenization and training -> implement/explain a tiny model with separate train/dev/test measurements, not another required course stack |
| Optional full run / cloud GPU | [karpathy/nanochat](https://github.com/karpathy/nanochat) | Materials available; price actual chosen compute before running | Tokenizer/pretrain/post-train pipeline -> one reproducible experiment only after setting a compute cap |
| Optional research specialization / home study + cloud GPU | [Stanford CS336](https://cs336.stanford.edu/) (Spring 2026) | Free materials; 100+ h full path estimate and GPU assignments | Requires calculus, linear algebra, probability, ML/PyTorch and systems readiness -> choose a lecture/assignment only after assessing prerequisites; follow the course's AI-use policy |

Knowledge check: explain LoRA versus full fine-tuning/QLoRA, memory still needed for frozen weights, development versus final test data, loss versus useful behavior and export compatibility. Generic loss thresholds do not prove overfitting or quality; do not turn an implementation guide's post-hoc split advice into evaluation practice.

Then apply: first establish a prompt-only baseline and retrieval baseline only where justified (§6). Freeze deduplicated train/dev/test partitions **before training**. Train one supported small model within an explicit cloud budget; compare base and adapter on the held-out task, check regressions, save/reload, export only if supported, then repeat evaluation after export. A negative result is valid learning evidence; keep/deploy the adapter only if it improves the intended quality/cost trade-off.

**Compute:** use the laptop for reading, tiny CPU exercises and small-model inference, not the model-sized LoRA run. Check your actual Google/Colab account benefits and [Colab's variable availability/limits](https://research.google.com/colaboratory/faq.html); neither a notebook link nor a consumer subscription guarantees a particular GPU or free run. Kaggle or [rented GPUs](https://www.runpod.io/pricing) are alternatives after checking current availability, VRAM, total cost and storage. No fixed free quota, training duration or price-per-experiment is assumed.

Last verified: Nov 5, 2026 (selected theory/implementation content; training, export compatibility and compute entitlement not executed/confirmed).

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
Base commit: [BASE]. Review the exact base-to-candidate diff and relevant code/tests/results.
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

### Role file example (home only: `.claude/agents/reviewer.md`)
```markdown
---
name: reviewer
description: Reviews a given commit against acceptance criteria. Use after implementation.
tools: Read, Grep, Glob
model: inherit
---
You review code. You never edit files. Follow docs/agents/reviewer-assignment.md.
```

The caller supplies the exact diff and isolated-runner check results as readable artifacts. Do not add Bash/test execution, editing, persistent-write memory, write-capable MCP tools or delegation to a strictly read-only reviewer. Tests/builds execute project-controlled code and belong in the separate isolated runner. Work uses the corresponding approved Copilot read/search-only configuration, not this Claude file.

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

Study the experiment/error-analysis portions of the [Evals FAQ](https://hamel.dev/blog/posts/evals-faq/) before interpreting comparisons. Start with >=3 baseline and >=5 matched team tasks; record task difficulty, base commit, model/harness versions, context/effort and checks. Isolate runs and avoid leaking previous answers. Keep the reviewer fixed when testing implementation routing, or explicitly vary only the reviewer when testing review diversity (§4). Repeat/match tasks and report uncertainty; these starter counts do not prove a causal improvement. Do not treat agreement, cached responses, star counts or tokens saved as correctness evidence.

Monthly: read release notes of your harnesses, re-run 3 benchmark tasks before switching model/config, delete stale instructions. Quarterly: check whether parallelism still saves effort; review credentials/sandbox.

---

## 11. Market demand evidence (why these items)

Target: North American applied/product AI engineering, not a research-scientist syllabus. Public employer-owned listings below were accessible on Nov 5. They demonstrate concrete requirements, not their market-wide prevalence; posting dates were not exposed on most pages, so access date is not publication date.

| Role / geography | Required or central work | Preferred / qualification |
|---|---|---|
| [Scale: Senior Frontier Agents Engineer](https://job-boards.greenhouse.io/scaleai/jobs/4720478005), San Francisco | Production Python/LLM systems, quantitative evaluation, reliable tool/agent integration | RAG, cloud including Azure and fine-tuning are preferred; building multi-agent systems is not the same as operating coding-agent teams |
| [OpenAI: Applied AI Engineer, Enterprise](https://jobs.ashbyhq.com/openai/01091aed-427d-4e10-8cdb-fb500cf55bb9), US | Python, prototype-to-production delivery, systematic evaluation, security/governance, observability and cost/performance | Location metadata includes remote but the body specifies SF/NYC office attendance; verify eligibility rather than assuming remote |
| [Cohere: Forward Deployed Engineer, Agentic Platform](https://jobs.ashbyhq.com/cohere/b0bcef37-1d20-414f-aade-c54942d63df9), US/Canada | Production Python, RAG/agent applications, evaluation and customer-facing delivery | Travel is part of the role; orchestration familiarity is not a requirement for a particular named framework |
| [Cohere: Senior Full-Stack Engineer, North Admin](https://jobs.ashbyhq.com/cohere/c12e3636-ed16-47e7-ba1f-82860f88d340), Canada plus London | Production Python/React, enterprise RAG/agent features, debugging and security/privacy | A useful full-stack-to-AI bridge; the listing expects substantial deployed-system experience |

This four-role, three-AI-vendor convenience sample is not representative of the whole US/Canada market. None explicitly mandates .NET, a named orchestration product, multi-agent coding workflows or a certificate. That is not evidence these are absent elsewhere. Keep .NET/Azure for your existing leverage and add bounded production Python evidence, not beginner programming study.

| Background signal | Source / limits |
|---|---|
| Agents and RAG appear prominently in one board's June 2026 analysis (4,155 global postings; about 47% and 40%) | [InterviewStack](https://interviewstack.io/blog/how-ai-is-changing-ai-engineer-2026); the [May cross-post](https://dev.to/gnana_6392e836fd500a957dc/ai-engineer-skills-companies-want-in-2026-3449-posting-analysis-5f9l) is the same publisher, not independent corroboration. These are global, not North American rates |
| Agentic/retrieval skills grew in 2025 US postings | [Lightcast's April 2026 summary](https://lightcast.io/resources/blog/stanford-ai-2026) and [Stanford AI Index 2026](https://hai.stanford.edu/assets/files/ai_index_report_2026.pdf) use the same underlying data. Publication year is 2026; observations are 2025. The PDF was checked in the Nov 2 pass, not re-extracted here |
| Production agents, observability and evaluations are reported by practitioners | [LangChain survey](https://www.langchain.com/state-of-agent-engineering): n=1,340, fielded Nov-Dec 2025. Self-selected/vendor-skewed adoption evidence, not hiring percentages or a US/Canada sample |
| Coding-agent adoption and broad AI hiring context | Retained historical references: [JetBrains 2026](https://blog.jetbrains.com/research/2026/08/ai-coding-agent-adoption-2026/), [Q1 agentic-coder survey](https://nikolasburk-com.pages.dev/blog/state-of-agentic-coders-results/), [LinkedIn August 2026 report](https://delivery-p143253-e1476319.adobeaemcloud.com/adobe/assets/urn:aaid:aem:ef153078-1061-4817-82e7-a1c027d7a7d7/original/as/AI-Labor-Market-Update-August-2026-v2.pdf). Nov 2 checks retained; do not combine their populations into a skills ranking |

**Learning priorities, not a numerical market ranking:** production LLM/tool integration, Python, retrieval literacy, evaluation and operational ownership are the core applied track. Azure/.NET are your fit-specific implementation choices. MCP is transferable integration knowledge; A2A, distributed multi-agent systems, local serving and fine-tuning are conditional depth. Coding-agent teamwork improves your workflow only if measured; it is not established as a universal hiring baseline. The previously quoted fine-tuning percentage lacked a primary citation and is removed.

Quarterly or before a career move, sample employer-owned US/Canada listings for your actual role/seniority; record required versus preferred, location/remote/travel constraints, publication/access dates and duplicates. Do not infer demand from downloads, stars, course marketing or certification availability.

Last verified: Nov 5, 2026 for the four role examples and qualified public analyses; historical sources retain their stated earlier check.

### Certificates (optional; only if your employer values them)

| Cert | Scope / decision (Nov 2026) |
|---|---|
| [GH-300 GitHub Copilot](https://learn.microsoft.com/en-us/credentials/certifications/github-copilot/) | Offered; matches workflow/governance knowledge. Official Learn prep is the default; [DataCamp prep](https://www.datacamp.com/tracks/github-copilot-fundamentals-gh-300) remains a valid optional subscription path if you choose the exam. A track-completion badge is not the GitHub certification |
| [AI-103: Developing AI Apps and Agents on Azure](https://learn.microsoft.com/en-us/credentials/certifications/azure-ai-apps-and-agents-developer-associate/) | Offered; Python/Foundry and broader Azure AI app topics, not only agents. Choose only if the role/employer values this scope; exam and cloud labs have separate costs |

Pick at most one, after Phase E, only if valued by your employer/target role. No certification is required for the study path.

Watch, not a booking recommendation: [Claude Certified Architect - Foundations](https://anthropic-partners.skilljar.com/claude-certified-architect-foundations-certification). The official catalog/FAQ restricts it to Anthropic Partner companies; your eligibility and career value are unconfirmed. Its public topic list does not justify adding a required certificate or work-side Anthropic use.

---

## 12. What changed vs the two source files

| Topic | Decision | Reason |
|---|---|---|
| Codex inside Claude Code | Included (codex-plugin-cc) | Official OpenAI plugin since Mar 2026; the Copilot file missed it |
| Cross-model review | Optional measured diversity; independent context/checks required | Different vendors do not guarantee independent errors; approved same-vendor review is valid |
| LangGraph | Conditional Python stateful-service path | Valid concepts and current implementation docs; not an age-based rejection or coding-team prerequisite |
| MCP | General concepts/course -> current protocol -> C# implementation | SDK documentation does not replace protocol education; HF/Advanced courses retained as qualified alternatives/depth |
| Evals | DLAI + Hamel + implementation/reporting; Maven optional depth | Start with the first API behavior; paid or overlapping material is not inherently invalid |
| Local models | Optional C, one measured small-model lab | Does not delay application-building; hardware/model-class promises replaced by measurements |
| Building models | Readiness -> post-training/PEFT -> data/training -> export/evaluation | Knowledge precedes each exercise; Karpathy/HF/CS336 retain valid optional depth, not a compulsory theory stack |
| Handoff / communication | Explicit mechanisms + Spec Kit + templates | Your main gap; neither file answered it directly |
| Security | Phase 0 before tools; application checks in E | Instructions and read-only labels are not runtime isolation |
| Windows | Native; WSL2 only for agent-team split panes | Resolves the two files' disagreement |
| Sequence / materials | 0 -> A -> B -> E -> D; C/F optional | Every required slice has study, knowledge check and application; one continuing project, no fixed calendar gates |
| Currency / removals | Valid in 2026, not necessarily recent | Preserve evergreen material and completed work; consolidate duplicates, remove unsupported claims/shopping detail, and mark unverified items as watch |

---

## 13. Completion checklist

**Prerequisites (Phase 0)**
- [ ] §1.1 security material studied; trust, secrets and enforced permissions explained before tool access
- [ ] §3.6 access/model-policy questions and API/cloud budgets recorded in PROGRESS.md; pending optional surfaces do not block approved IDE study
- [ ] Existing VPS audited against §5.1 before expanding Hermes automation; patches/backups and actual running version checked

**Single agent (Phase A)**
- [ ] Repo has CLAUDE.md / AGENTS.md / copilot-instructions.md with exact build/test commands
- [ ] One genuinely useful customization/skill applied and its actual loading/effect checked
- [ ] Baseline: §10 metrics for >=3 single-agent tasks and a verified feature

**Coding-agent team (Phase B)**
- [ ] Implementer and reviewer role files committed; tester and planner only if the metrics justified them
- [ ] Reviewer has only read/search capabilities; builds/tests run separately in isolation
- [ ] If parallel writers are useful, worktrees have explicit bases and separate ports/DBs
- [ ] Every handoff names base/candidate commits and checks not run; integrated revision checked
- [ ] Work uses confirmed non-Anthropic paths; vendor diversity/CLI/Agent HQ are optional, not exit gates
- [ ] A seeded failing check blocks merge through CI/repository protection; hook limitations tested if used
- [ ] Initial §10 comparison over >=5 matched tasks, reporting setup, uncertainty and useful/false/missed findings

**Building agents (Phase E)**
- [ ] E1: conceptual study/knowledge check completed; one bounded user workflow and baseline specified
- [ ] E2: native API loop, validated structured output, authorized tool calls and failure/round/context/deadline limits
- [ ] E3: error analysis, starter regression cases, separate holdout, human-calibrated judges if used; CI failure and traces demonstrated
- [ ] E4: general MCP understood before the C# SDK; two read-only .NET tools used from a compatible host; MAF approval/state/recovery checked
- [ ] E5: retrieval fundamentals and evaluation understood; RAG feature, authorization and document lifecycle tested if included
- [ ] E6: resilience/security failures tested; small reproducible Python component; container/cloud release, identity, health/rollback, retained telemetry and runbook demonstrated

**Scripted orchestration (Phase D)**
- [ ] Headless/permission/state/process material studied after the manual workflow is repeatable
- [ ] Implement -> tests -> fresh review -> human approval with persisted task/commit identity, bounded repairs, aggregate limits and verified cancellation/recovery
- [ ] Optional only: Hermes/Telegram trigger and notifications added if selected, never involving work data

**Local inference (optional Phase C)**
- [ ] Memory/quantization/API knowledge check completed; one compatible small local model measured on a labeled batch at two context sizes
- [ ] Actual RAM, cold/warm latency, prefill/generation speed and quality recorded; extra runtimes/models not required

**Models (optional Phase F)**
- [ ] Readiness established or §8.1 bridge completed before training; post-training/LoRA/data/evaluation concepts explained
- [ ] Data rights checked and leakage-resistant train/dev/test partitions frozen before a budgeted cloud-GPU run
- [ ] Base/adapter comparison recorded, including regressions and cost; compare the served/exported artifact too if supported, otherwise record the limitation. Keep adapter only if justified
- [ ] Optional only: deeper Karpathy/HF/nanochat/CS336 study selected after assessing its prerequisites
