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
| Q8 | Is LangGraph still relevant in 2026? | Yes, in Python agent apps, but not needed for running coding agents. For .NET, Microsoft Agent Framework comes first. | 6.3 |
| Q9 | What does the market actually ask for? | Agents, LLM integration, RAG, MCP, evals and observability, Python, Azure. Fine-tuning and certificates come much lower. | 11 |

---

## 1. Plan at a glance

At about 8 hours/week:

| Phase | Weeks | Goal | Exit criterion |
|---|---|---|---|
| A. Single-agent mastery | 1–2 | One harness configured properly on your repo | A feature shipped via plan → implement → verify with repo instruction files and two skills |
| B. Coding-agent team | 3–6 | Implementer, tester and reviewer roles in worktrees, cross-model review, hook/CI gates | One React + .NET feature through the full loop, with metrics recorded |
| C. Multi-model + local | 7–8 | Route roles to different models, including one local model | Same task run on 2–3 model setups with cost/quality compared |
| D. Scripted orchestration | 9–10 | A script runs implement → test → review without you | Timeouts, retry limits and approval stop work; failures are visible |
| E. Building agents | Months 3–5 | Agent apps in .NET (and Python if needed), MCP servers, evals, observability, security | MCP server + agent + 30-case eval suite running in CI |
| F. Models | Month 6+ | Fine-tune a small open model; optionally learn transformers from scratch | A LoRA adapter that beats the prompt-only baseline on your eval set |

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
- [Building effective agents, Anthropic](https://www.anthropic.com/engineering/building-effective-agents): workflows vs agents; the five base patterns (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer). This is the vocabulary every framework reuses.
- [A practical guide to building agents, OpenAI (PDF)](https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf): single-agent vs manager vs decentralized handoff designs, guardrails.
- [Effective context engineering for AI agents, Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents): what each worker should and shouldn't see.

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

1. **Parent ↔ child (subagents).** The parent sends a task prompt; the child works in fresh context and returns one final message. Children don't talk to each other.
   - [Claude Code subagents](https://code.claude.com/docs/en/sub-agents) · [Codex subagents](https://developers.openai.com/codex/multi-agent) · [Copilot custom agents](https://docs.github.com/en/copilot/concepts/customizations/custom-agents)
2. **Shared task list + messages (agent teams).** A lead creates tasks; teammates claim them, message each other, and hooks (`TaskCreated`, `TaskCompleted`, `TeammateIdle`; exit code 2 blocks) can reject work. Still experimental as of Nov 2026: enable it with `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. Teams need an interactive session (none under `-p`). Build on subagents plus hooks first (`model:`, `isolation: worktree`), and add teams second.
   - [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams)
3. **Artifacts in the repo.** Spec files, plan files, progress notes, commits, PRs, CI results. This works across any tools and models, and it's the most reliable mechanism.
   - [Effective harnesses for long-running agents, Anthropic](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents): progress file + git history as memory between sessions.
   - [GitHub Spec Kit](https://github.com/github/spec-kit): spec → plan → tasks as files that any agent (Claude, Codex, Copilot) consumes. This is "spec-driven development", the common 2026 handoff format.

In code frameworks the same ideas exist as named patterns: **sequential, concurrent, handoff, group chat, manager (Magentic)**.
- [Microsoft Agent Framework orchestrations](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations)
- [OpenAI Agents SDK handoffs](https://openai.github.io/openai-agents-python/handoffs/)
- [How Anthropic built its multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system): real lessons on lead/worker delegation, token cost (~15× chat), and when multi-agent fails.

### 3.3 How they hand off work

A handoff is valid only if it names an **exact commit** and says what was and wasn't verified. Use the templates in section 9 unchanged. Rules:
- The reviewer gets requirements + diff, **not** the implementer's explanation (it biases the review).
- If the candidate commit changes, earlier review/test results are void.
- Each implementer owns specific files; two writers on one file = conflicts.
- Contract first: agree on request/response types and error cases before parallel FE/BE work.

### 3.4 How to orchestrate (three levels)

| Level | Who orchestrates | Use when | Learn from |
|---|---|---|---|
| 1. Manual | You run sessions and pass artifacts | Weeks 3–6; always first | Sections 3.3 and 9 |
| 2. Harness | Main Claude Code session + subagents / agent team; Copilot coding agent per issue | Repeatable roles in one tool | [Subagents](https://code.claude.com/docs/en/sub-agents), [Agent teams](https://code.claude.com/docs/en/agent-teams), [Copilot cloud agent best practices](https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results) |
| 3. Code | Your script/service launches workers | Manual flow already works repeatedly | [Claude Code headless](https://code.claude.com/docs/en/headless), [Codex non-interactive](https://developers.openai.com/codex/noninteractive), [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview) |

**Gates are code, not prompts:** tests, type checks, linters, [Claude Code hooks](https://code.claude.com/docs/en/hooks) (exit code 2 blocks the action and returns the error to the agent), GitHub branch protection + required checks.

### 3.5 Materials for phases A–B

| Item | Cost · time | Why |
|---|---|---|
| [Claude Code in Action](https://anthropic.skilljar.com/claude-code-in-action), plus Subagents and Agent Skills courses at [Claude Academy](https://academy.claude.com) | Free · 5–8 h | CLAUDE.md, permissions, hooks, subagents, skills |
| [Claude Code: A Highly Agentic Coding Assistant (DeepLearning.AI)](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant) | Free · 2 h | Worktrees, parallel sessions, GitHub integration, hands-on |
| [Claude Code common workflows (worktrees)](https://code.claude.com/docs/en/common-workflows) + [git worktree](https://git-scm.com/docs/git-worktree) | Free · 1 h | Isolation for parallel writers |
| [Claude Code skills](https://code.claude.com/docs/en/skills) · [AGENTS.md](https://agents.md) | Free · 1 h | Shared instruction format; AGENTS.md is read by Codex, Copilot and others |
| [Copilot Fundamentals](https://learn.microsoft.com/en-us/training/paths/copilot/) + [Copilot coding agent module](https://learn.microsoft.com/en-us/training/modules/github-copilot-code-agent/7-summary) + [Copilot instructions & custom agents](https://learn.microsoft.com/en-us/training/modules/configure-customize-github-copilot-visual-studio-code/1-introduction) | Free · 6–8 h | Your work stack: `.github/copilot-instructions.md`, `.github/agents`, issue → PR |
| [Codex docs: subagents](https://developers.openai.com/codex/multi-agent), [Windows/sandbox](https://developers.openai.com/codex/windows) | Free · 2 h | Codex as your second harness / reviewer |

Windows note: everything works natively except the agent-teams split-pane view, which needs tmux (use WSL2 or the default in-process mode).

**Shared instruction format for both machines:** use `AGENTS.md` (Copilot, Codex, Hermes and others read it; Claude Code via `CLAUDE.md`) plus skills in `.agents/skills` or `.github/skills`. Copilot's coding agent also reads `CLAUDE.md` and `GEMINI.md`, so keep Anthropic-specific instructions out of work repos.

### 3.6 Work-side team: Copilot only, no Anthropic models (validated Nov 2, 2026)

Everything in 3.1–3.4 maps onto Copilot Business/Enterprise:

| Concept | Copilot equivalent |
|---|---|
| Role file | [Custom agent](https://docs.github.com/en/copilot/reference/custom-agents-configuration) `.github/agents/<role>.agent.md`: `name`, `description`, `tools`, **`model`**, `mcp-servers`, `handoffs` (`handoffs` is ignored on github.com). Works in VS Code, Copilot CLI and the github.com coding agent; repo-, org- or enterprise-level |
| Parallel subagents | Copilot CLI `/fleet` (since Apr 2026): parallel subagents with their own contexts that can use your `.github/agents` roles. No messaging between them |
| Cloud implementer | Copilot coding agent (issue → PR), and **Codex via Agent HQ** as a second cloud agent (Business since Feb 26, 2026; one premium request per session). Keep the **Claude** partner agent off |
| Cross-model review | Pin a different vendor per role. Example: implementer = GPT-6.x or GPT-5.3-Codex, reviewer = Gemini 4 Argon, optional tie-break reviewer = Grok or Kimi. Check the [live model list](https://docs.github.com/en/copilot/reference/ai-models/supported-models); names change often |
| PR review | Copilot code review, driven by `copilot-instructions.md`, is one reviewer. Your own `reviewer.agent.md` on another model is a separate step; there's no official way to merge the two |
| Gates | GitHub Actions, branch protection, required checks |

**Copilot has several surfaces, each with its own admin policy.** Having Copilot doesn't mean you have all of them:

| Surface | What it is | How to check you have it |
|---|---|---|
| IDE chat / agent mode | Copilot inside VS Code or Visual Studio. Custom agents (`.github/agents`) work here | You're using it today |
| **Copilot CLI** | A separate terminal tool (`copilot`), GA since Feb 2026. This is where `/fleet` and headless scripting live | Install it per the [Copilot CLI docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli) and sign in. If the org policy is off, it says so |
| Coding agent (cloud) | Assign an issue to Copilot on github.com → it opens a PR | "Copilot" appears in an issue's Assignees list |
| Agent HQ partner agents | Codex (and Claude, keep off) as cloud agents | They appear next to Copilot in the agent picker on github.com |
| Copilot code review | Copilot as a PR reviewer | You're using it today |

**Your org's models (as you reported, Nov 2026):** GPT-6.1 (Sol, Terra), GPT-6 Sol/Luna and other recent OpenAI models; not GPT-6 Astra. **Still to check: are any non-OpenAI models enabled (Gemini 4 Argon, Grok, Kimi, MAI)?**
- **If yes:** pin a different vendor for the reviewer, as in the table above.
- **If OpenAI only:** true cross-vendor review isn't possible at work. Get the closest equivalent:
  - Use a different model *tier* for the reviewer than the implementer (e.g. implementer GPT-6.1 Sol, reviewer GPT-6.1 Terra or GPT-6 Sol).
  - Give the reviewer a fresh context and a read-only tool set.
  - Add Copilot code review as an independent second pass.
  - Rely on tests and CI as the real gate.
  - Measure whether this catches defects (§10) rather than assuming it does.

**Ask your admin about** (only the ones you don't already have): the Copilot CLI policy, the cloud (coding) agent, the Codex partner-agent policy, and any non-OpenAI models. Anthropic models and the Claude agent stay disabled; per-model toggles handle this.

Exercise (work): create `implementer`, `tester` and `reviewer` agent files with three different pinned models, run one real ticket through `/fleet` → PR → Copilot code review + your reviewer agent, and record the §10 metrics.

Exercise (Phase B): implement a React list + .NET endpoint feature. Planner writes `spec.md` with the API contract → two implementers in two worktrees → tester writes tests from the spec → Codex reviews the exact merge commit → you accept/reject each finding → CI merges. Record the metrics from section 10.

---

## 4. Working with different models together (Q5)

| Technique | What it is | How to do it |
|---|---|---|
| Cross-model review | Implementer and reviewer use different vendors' models; they make fewer shared mistakes | [openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc): official OpenAI plugin for Claude Code: `/codex:review`, `/codex:adversarial-review`, optional review gate on stop. Works with a ChatGPT plan or API key |
| Per-role model choice | Strong model for planning/review, cheaper/faster model for tests, search, boilerplate | `model:` field in the [subagent](https://code.claude.com/docs/en/sub-agents) file; model selection per Copilot custom agent |
| Multi-vendor agents on GitHub | Claude and Codex as assignable agents on issues/PRs next to Copilot | GitHub Agent HQ (Pro, Pro+, Business, Enterprise; admin enables each partner agent). **At work: Codex only** (see 3.6) |
| Gemini as a home reviewer | Your Google AI Pro plan works with **Antigravity CLI (`agy`)** (announced on the Google Developers Blog, "transitioning Gemini CLI to Antigravity CLI"), not Gemini CLI. Google moved Gemini CLI to `agy` at I/O (May 19, 2026), and Gemini CLI stopped serving AI Pro/Ultra on Jun 18, 2026 | Use `agy` interactively, or hand it the exact diff manually, as an optional third reviewer. Wrapping or scripting `agy` on a consumer plan has unclear terms, so for scripted use take a paid Gemini API key (Gemini CLI still works with one). Claude Code ↔ Gemini MCP bridges are community projects only, not official |
| Home cross-model reviewer (main) | `openai/codex-plugin-cc`, official, ~35k stars | Needs Node 18.18+ and `npm i -g @openai/codex`, then `/codex:setup`. ChatGPT Free/Go gives light access, enough for a trial; Plus or an API key is needed for regular use |
| Provider-neutral API layer | One interface, swap providers by config | .NET: [Microsoft.Extensions.AI](https://learn.microsoft.com/en-us/dotnet/ai/microsoft-extensions-ai) (`IChatClient`). Python: [LiteLLM](https://docs.litellm.ai). Hosted router: [OpenRouter](https://openrouter.ai/docs) |
| Model comparison by evidence | Same task, same acceptance criteria, different setups | [AI Coding Workflows: From Cloud to Local (DeepLearning.AI + JetBrains)](https://www.deeplearning.ai/courses/ai-coding-workflows-from-cloud-to-local): free, 1.5 h; swaps Claude Code ↔ OpenCode, routes via OpenRouter, ends with a local model in LM Studio |

Two models agreeing doesn't prove the code is correct. Tests and your judgment decide.

---

## 5. Scripted orchestration (Phase D)

Build a small coordinator **around existing harnesses**; don't rewrite the agent loop.

```text
task intake → validate scope → git worktree add → launch implementer (headless, JSON output)
→ collect result + commit SHA → run tests/CI → launch reviewer on that SHA
→ ≤2 repair rounds → stop for human approval
```

Your coordinator must handle: task IDs and state, exit codes, timeouts, cancellation, retry limits, commit identity (re-review after any change), cost/concurrency caps, secrets kept out of worker env, escalation to you.

| Resource | Use |
|---|---|
| [Claude Code headless / `-p`](https://code.claude.com/docs/en/headless) | `--output-format json`, session IDs, permission flags |
| [Codex non-interactive (`codex exec`)](https://developers.openai.com/codex/noninteractive) | Same for Codex |
| [Claude Agent SDK (Python/TS)](https://code.claude.com/docs/en/agent-sdk/overview) | Claude Code's harness as a library: subagents, hooks, MCP, permissions in code |
| [OpenAI agent runtime options](https://developers.openai.com/api/docs/guides/agents) | Agents SDK vs Responses API vs managed agents: who runs the loop and stores state |

Note: SDK/API use is billed separately from interactive subscriptions. Check auth and terms before automating. `--bare` is recommended for scripts and needs an API key.

**Hermes Agent's place (home):** use it as the Telegram front door: kicking off jobs, notifications, triage. It doesn't drive Claude Code or Codex natively, so have it trigger `claude -p --output-format json` or `codex exec` through a script or MCP tool, and report the result back. Don't make it the code orchestrator. It supports MCP, agentskills.io skills, and per-subagent provider and model.

---

## 6. Building your own agents (Phase E)

### 6.1 Foundations (pick in this order)

| # | Item | Cost · time | What you get |
|---|---|---|---|
| 1 | [Agentic AI, Andrew Ng (DeepLearning.AI)](https://www.deeplearning.ai/courses/agentic-ai/) | Audit free; graded work Pro · ~20 h | Reflection, tool use, planning, multi-agent, error analysis in plain Python, no framework |
| 2 | [Building with the Claude API (Claude Academy)](https://academy.claude.com) / [Claude with the Anthropic API](https://anthropic.skilljar.com/claude-with-the-anthropic-api) | Free · ~8 h | Tool use, structured output, RAG, prompt caching, agent loop from first principles |
| 3 | [Writing effective tools for agents, Anthropic](https://www.anthropic.com/engineering/writing-tools-for-agents) | Free · 1 h | Tool naming, descriptions, response shaping: the most common cause of bad agents |

### 6.2 .NET first: Microsoft Agent Framework (your highest-leverage build skill)

Successor to Semantic Kernel + AutoGen (AutoGen is in maintenance mode). Built-in orchestrations: sequential, concurrent, handoff, group chat, Magentic.

| Item | Cost · time |
|---|---|
| [Agent Framework overview](https://learn.microsoft.com/en-us/agent-framework/overview/) + [orchestrations](https://learn.microsoft.com/en-us/agent-framework/workflows/orchestrations) | Free · 3 h |
| [Develop AI agents on Azure (MS Learn path)](https://learn.microsoft.com/en-us/training/paths/develop-ai-agents-on-azure/) | Free · ~10 h |
| [Generative AI for Beginners .NET](https://github.com/microsoft/generative-ai-for-beginners-dotnet) | Free · 5–8 h: MAF, Microsoft.Extensions.AI, vector data |
| [.NET AI docs](https://learn.microsoft.com/en-us/dotnet/ai/overview) | Reference |

Check package status: parts of Agent Framework (notably some Python packages, e.g. `agent-framework-orchestrations`) are still prerelease. Pin versions.

### 6.3 Python: LangGraph, yes or no? (Q8)

- **Still relevant.** In 2026 LangGraph is among the most-downloaded Python agent-orchestration frameworks, and it shows up explicitly in job postings (about 4.3k US AI postings in 2025, Stanford AI Index). Thoughtworks' Technology Radar says it is no longer the default for *every* agent system.
- **Not needed for Phases A–D.** Coding-agent teams run on Claude Code / Codex / Copilot, not on LangGraph.
- **Learn it if** you build stateful Python agent services (checkpoints, human-in-the-loop, durable execution).
- **Which course:** "Quickstart: LangGraph Essentials (Python)" at [LangChain Academy](https://academy.langchain.com) (free) + [LangGraph docs](https://docs.langchain.com/oss/python/langgraph/overview). Skip the older "Introduction to LangGraph" course, which predates v1.
- **Alternative:** [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) (lighter, handoffs built in). Pick **one** Python framework.

### 6.4 MCP (tools) and A2A (agent ↔ agent)

| Item | Cost · time | Why |
|---|---|---|
| [Introduction to MCP + MCP Advanced Topics (Claude Academy)](https://academy.claude.com) | Free · ~4 h | Tools/resources/prompts, transports, Inspector, production patterns |
| [Hugging Face MCP Course](https://huggingface.co/learn/mcp-course/en/unit0/introduction) | Free · ~6 h | Hands-on, free certificate |
| [MCP spec & docs](https://modelcontextprotocol.io/docs/getting-started/intro) · [C# MCP SDK](https://github.com/modelcontextprotocol/csharp-sdk) · [Claude Code MCP](https://code.claude.com/docs/en/mcp) | Free | Build servers in .NET, use them from every harness |
| [A2A protocol](https://a2a-protocol.org) | Free · 2 h | v1.0 stable, Linux Foundation; Agent Cards, tasks, streaming. Learn once agents live in separate services |

### 6.5 Evals and observability (the skill that separates demos from production)

| Item | Cost · time | Why |
|---|---|---|
| [Evaluating AI Agents (DeepLearning.AI + Arize)](https://www.deeplearning.ai/short-courses/evaluating-ai-agents/) | Free · ~3 h + practice | Traces, component vs end-to-end evals, LLM-as-judge |
| [LLM Evals FAQ, Hamel Husain](https://hamel.dev/blog/posts/evals-faq/) | Free · 2 h | Error analysis first, then judges; the free core of the paid course |
| [AI Evals for Engineers & PMs (Maven)](https://maven.com/parlance-labs/evals) | ~$4,200–5,000 list (discounts common; check dates) | Deep version; ask your employer to pay. Optional |
| [OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) | Free · 1 h | Standard trace format for LLM/agent calls |
| [Langfuse docs](https://langfuse.com/docs) (open source) or LangSmith | Free tier | Trace viewer, datasets, eval runs. In .NET, the Aspire dashboard shows OTel traces |

### 6.6 Security (do this before agents get shell/git/network access, so in Phase B)

- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/): goal hijack, tool misuse, memory poisoning, least agency.
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/): prompt injection is still #1.
- [Red Teaming LLM Applications (DeepLearning.AI)](https://www.deeplearning.ai/short-courses/red-teaming-llm-applications/): free, 1 h intro.
- Practical rules: least-privilege tools, no production secrets in agent env, reviewer read-only via permissions (not via the prompt), treat issues/docs/tool output as untrusted input, cap retries and spend.

### 6.7 RAG (only when an agent needs private or changing knowledge)

- [Retrieval Augmented Generation (DeepLearning.AI)](https://www.deeplearning.ai/courses/retrieval-augmented-generation-rag/): chunking, BM25 + vector hybrid search, reranking, grounding evals. RAG is in about 40% of AI-engineer postings (section 11).

Phase E capstone: a .NET MCP server over one internal API → an Agent Framework agent using it → 30 eval cases in CI → OTel traces in Aspire/Langfuse → OWASP checklist passed.

---

## 7. Running models locally (Q6)

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
| [Ollama](https://docs.ollama.com/) | **Your default.** Easiest server; OpenAI- **and** Anthropic-compatible API. On Windows, Vulkan is on by default and uses the Iris Xe GPU, but on weak iGPUs it's sometimes slower than CPU. Benchmark both and set `OLLAMA_IGPU_ENABLE=0` if CPU wins |
| [LM Studio](https://lmstudio.ai/docs) | GUI + local server (ships the llama.cpp Vulkan runtime); used in the DeepLearning.AI cloud-to-local course |
| [llama.cpp](https://github.com/ggml-org/llama.cpp) | The engine under most local tools; max control |
| [OpenVINO 2026.x](https://docs.openvino.ai) | Intel's own runtime (CPU/iGPU/NPU), OpenAI-compatible via Model Server. Optional experiment on your Intel laptop. (IPEX-LLM was archived Jan 2026, so skip it) |
| [vLLM](https://docs.vllm.ai) | High-throughput serving on a GPU server (team/production use); not for your laptop |

**Models for your laptop (Nov 2026):** `qwen3.5:4b` (main choice for tool calling and JSON), `qwen3.5:9b` (best quality that fits), `gemma4:e4b` (second opinion, multimodal). Set `num_ctx` to 16384–32768 explicitly. gpt-oss-20b (~14 GB) does not fit sensibly.

**Hardware:** don't buy now. Prices are inflated in 2026 (RTX 5090 street ~$3.7–5K, DGX Spark from ~$4.7K), and $50–100 of cloud GPU covers a year of learning. A cheap optional step is upgrading the T14 Gen 2's RAM to 32–40 GB, which allows slow 14B-class or MoE models. Confirm the slot layout on Lenovo PSREF first.

### 7.3 Local models inside your coding-agent team

- **Claude Code + local model:** [Ollama Anthropic compatibility](https://docs.ollama.com/api/anthropic-compatibility) (Ollama ≥ 0.14; ≥ 0.14.3 for streaming tool calls). Set `ANTHROPIC_BASE_URL=http://localhost:11434`, `ANTHROPIC_AUTH_TOKEN=ollama`, then `claude --model <tag>`, or use `ollama launch claude`. Values in Claude settings files can override these env vars. Hosted web search isn't available this way. **On your laptop, do this once to learn the setup, not as a live subagent.** Each turn first processes a 20–50K-token prompt, which takes minutes here, and 9B models edit poorly; ~20–27B is the practical minimum for agentic coding.
- **Codex / OpenAI-compatible tools:** point them at Ollama/LM Studio's OpenAI-compatible endpoint.
- **.NET apps:** `IChatClient` from Microsoft.Extensions.AI with the Ollama provider, so a config switch swaps local and cloud.
- **Realistic use:** on your laptop, local models fit offline batch jobs: summarizing logs and docs, commit-message and docstring drafts, test stubs generated by a background script, embeddings/RAG experiments, and learning the API and tool calling. Keep frontier models for every interactive role. Measure this; don't assume it.
- Local inference ≠ private by default: plugins, telemetry and MCP servers can still send data out.

Phase C exercise (home): run the same Phase-B task with (a) Claude only, (b) Claude implementer + a second-vendor reviewer (Codex via codex-plugin-cc, or Gemini via `agy`), (c) a local `qwen3.5:4b` generating test stubs as a background script. Compare accepted-change time, defects, cost. For a local model as a real interactive role, rent a GPU for a few hours and serve a ~27B model instead.

---

## 8. Building and adapting models (Q7)

Order matters. Each step has a concrete output.

| Step | Resource | Cost · time | Output |
|---|---|---|---|
| 1. How LLMs work, in code | [Neural Networks: Zero to Hero, Andrej Karpathy](https://karpathy.ai/zero-to-hero.html) | Free · 20–30 h | You write a GPT from scratch in PyTorch |
| 2. Transformers ecosystem | [Hugging Face LLM Course](https://huggingface.co/learn/llm-course/chapter1/1) | Free · 30–40 h | Tokenizers, datasets, fine-tuning with `transformers` |
| 3. Practical fine-tuning | [Unsloth docs](https://docs.unsloth.ai) (LoRA/QLoRA, fits a single consumer GPU or free Colab) + [Hugging Face TRL](https://huggingface.co/docs/trl) (SFT, DPO, GRPO) | Free · 10–15 h | A LoRA adapter on your own dataset, exported to GGUF and served by Ollama |
| 4. Post-training theory | [Post-training of LLMs (DeepLearning.AI)](https://www.deeplearning.ai/short-courses/post-training-of-llms/) | Free audit · ~2 h | When to use SFT vs DPO vs RL |
| 5. Full training run (optional) | [karpathy/nanochat](https://github.com/karpathy/nanochat) | ~$73–100 of rented GPU (~4 h on 8×H100) | Tokenizer → pretrain → SFT → chat UI, end to end |
| 6. Research depth (optional) | [Stanford CS336: Language Modeling from Scratch](https://cs336.stanford.edu/) | Free materials · 100+ h | Needs linear algebra, probability, PyTorch, GPU budget |

Before fine-tuning, always have (a) a prompt + RAG baseline and (b) a held-out eval set. Fine-tune only when it measurably beats the baseline. Fine-tuning appears in about 15% of AI-engineer postings, while agents/RAG/evals appear far more often (section 11). Treat this track as a long-term investment, not a job requirement.

**Where to run it (your laptop can't fine-tune):** Unsloth's free notebooks on Colab (T4, ~15 GB) or Kaggle (~30 GPU-h/week). A QLoRA run on a 4–8B model with ~1k examples takes 15–60 min, which is $0 there. If the T4 runs out of memory, rent an RTX 4090 (~$0.35–0.90/h) or L40S (~$0.80/h) on RunPod, under $5 per experiment. Export to GGUF and serve with `ollama create` to connect this track to §7.

Phase F exercise: fine-tune a 3–8B model (e.g. Qwen3.5-4B) with Unsloth on ~1,000 examples from your domain (e.g. your codebase's commit-message or API-doc style), serve it with Ollama, compare it with the base model on your eval set.

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
| AI agents in ~47% of AI postings (#3 skill); LLMs ~56%; RAG ~40% (4,155 postings, Jun 2026) | [InterviewStack analysis](https://interviewstack.io/blog/how-ai-is-changing-ai-engineer-2026) |
| Python 71%, LLMs 66%, RAG 40%, Azure 33%, monitoring 31%, LangChain 25% (3,449 postings, May 2026) | [DEV analysis](https://dev.to/gnana_6392e836fd500a957dc/ai-engineer-skills-companies-want-in-2026-3449-posting-analysis-5f9l) |
| 2025 US postings: agentic AI 16.5k, RAG 12.6k, LangGraph 4.3k | [Stanford AI Index 2026, pp. 204–208](https://hai.stanford.edu/assets/files/ai_index_report_2026.pdf) |
| US AI Engineer hiring +64% YoY (Apr–Jun 2026) while overall hiring −6.9% | [LinkedIn AI Labor Market Update, Aug 2026](https://delivery-p143253-e1476319.adobeaemcloud.com/adobe/assets/urn:aaid:aem:ef153078-1061-4817-82e7-a1c027d7a7d7/original/as/AI-Labor-Market-Update-August-2026-v2.pdf) |
| 57% have agents in production; 89% use observability; 52% run offline evals | LangChain State of Agent Engineering survey (n=1,340) |
| MCP: fast-growing requirement, few developers have hands-on experience | [Digital Applied hiring guide, May 2026](https://www.digitalapplied.com/blog/ai-developer-hiring-skills-that-matter-2026) |
| Fine-tuning in ~15% of postings; no evidence employers require CCA-F, AI-103 or GH-300 | Claude roadmap validation |

Numbers differ between sources (different boards and taxonomies), so treat them as direction, not precision. The consistent ranking is: **agents + LLM integration > RAG > cloud (Azure) > observability/evals > MCP > frameworks > fine-tuning > certificates.**

Multi-agent coding orchestration specifically is a **growing differentiator, not a baseline hiring requirement**. AI-agent use at work is mainstream (JetBrains 2026: 90% weekly), but a Q1 2026 survey found most developers don't run multi-agent orchestration yet. Build it on top of solid single-agent skills: context files, evals, review gates. (These figures come from secondary write-ups.)

### Certificates (optional; only if your employer values them)

| Cert | Status (Nov 2026) |
|---|---|
| [AI-103: Developing AI Apps and Agents on Azure](https://learn.microsoft.com/en-us/credentials/certifications/) | Beta; replaced AI-102 (retired Jun 30, 2026). Best fit if your employer is on Azure |
| [Claude Certified Architect – Foundations](https://anthropic.skilljar.com/claude-certified-architect-foundations-access-request) | $125; agentic architecture, Claude Code config, MCP, context |
| GH-300 GitHub Copilot ([DataCamp prep](https://www.datacamp.com/tracks/github-copilot-fundamentals-gh-300)) | Covers agent mode, MCP, CLI |

Pick at most one, after Phase E.

---

## 12. What changed vs the two source files

| Topic | Decision | Reason |
|---|---|---|
| Codex inside Claude Code | Included (codex-plugin-cc) | Official OpenAI plugin since Mar 2026; the Copilot file missed it |
| Cross-model review | Default, but measured | Claude file: "deliberate"; Copilot file: "no universal rule". Both apply |
| LangGraph | "Essentials" course, Python-only, after MAF | Old intro course predates v1; not needed for coding-agent teams |
| MCP | Phase E (build), used from Phase A | Needed for building; just consumed while operating |
| Evals | Free DeepLearning.AI + Hamel's FAQ first; Maven optional | Maven price/dates vary ($4.2–5k); free material covers the core |
| Local models | Own phase (C) + Ollama → Claude Code integration | Claude file had almost nothing on local inference |
| Building models | Karpathy → HF → Unsloth/TRL → nanochat → CS336 | Neither file had a hands-on path from zero to a fine-tuned model |
| Handoff / communication | Explicit mechanisms + Spec Kit + templates | Your main gap; neither file answered it directly |
| Security | Moved to Phase B | Agents get shell/git access from the start |
| Windows | Native; WSL2 only for agent-team split panes | Resolves the two files' disagreement |

---

## 13. Completion checklist

**Operating a coding-agent team (Phases A–D)**
- [ ] Repo has CLAUDE.md / AGENTS.md / copilot-instructions.md with exact build/test commands
- [ ] Two skills and three role files (implementer, tester, reviewer) committed
- [ ] Reviewer is read-only via permissions, not via prompt
- [ ] Parallel work uses worktrees with separate ports/DBs
- [ ] Every handoff names an exact commit and lists checks not run
- [ ] Cross-model review set up: home = codex-plugin-cc (optionally Gemini via `agy`); work = role agents pinned to different non-Anthropic models + Agent HQ Codex
- [ ] Hooks/CI block merges on failing checks
- [ ] Metrics recorded for ≥5 tasks: single agent vs team
- [ ] One local model used in a real role
- [ ] A script runs implement → test → review with timeouts and retry limits

**Building agents (Phase E)**
- [ ] Wrote an agent loop without a framework (Claude/OpenAI API)
- [ ] Built and used a .NET MCP server
- [ ] Built an Agent Framework workflow using at least two orchestration patterns
- [ ] 30-case eval suite in CI; traces visible in OTel tooling
- [ ] OWASP agentic Top 10 reviewed against your agent

**Models (Phase F)**
- [ ] Ran 3 local models and compared them on your eval set
- [ ] Fine-tuned a LoRA adapter, served it via Ollama, measured vs base
