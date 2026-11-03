# Generalized Approach: AI Agent Engineering Roadmap (Nov 2026)

Prepared: November 2, 2026
For: a senior developer (React, .NET, Python) who uses AI tools daily and wants to (1) run a team of coding agents, (2) build agents and orchestration, (3) run and adapt models locally.
Merges: `Claude - Agentic AI Learning Path 2026.md` and `Copilot - AI-agent-workflow-learning-roadmap.md`, with corrections from a fresh source check.

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
2. **Shared task list + messages (agent teams).** A lead creates tasks; teammates claim them, message each other, and hooks (`TaskCompleted`, `TeammateIdle`) can reject work. Experimental, off by default.
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

Exercise (Phase B): implement a React list + .NET endpoint feature. Planner writes `spec.md` with the API contract → two implementers in two worktrees → tester writes tests from the spec → Codex reviews the exact merge commit → you accept/reject each finding → CI merges. Record the metrics from section 10.

---

## 4. Working with different models together (Q5)

| Technique | What it is | How to do it |
|---|---|---|
| Cross-model review | Implementer and reviewer use different vendors' models; they make fewer shared mistakes | [openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc): official OpenAI plugin for Claude Code: `/codex:review`, `/codex:adversarial-review`, optional review gate on stop. Works with a ChatGPT plan or API key |
| Per-role model choice | Strong model for planning/review, cheaper/faster model for tests, search, boilerplate | `model:` field in the [subagent](https://code.claude.com/docs/en/sub-agents) file; model selection per Copilot custom agent |
| Multi-vendor agents on GitHub | Claude and Codex as assignable agents on issues/PRs next to Copilot | GitHub Agent HQ (Copilot Pro+/Enterprise) |
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

Note: SDK/API use is billed separately from interactive subscriptions. Check auth and terms before automating.

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
| VRAM math | Rough guide: Q4 ≈ 0.6 GB per billion params + context (KV cache). A 32 GB laptop handles about 12–30B models |
| Context length | Local servers often default low; coding agents need ≥32–64K |
| Tool-calling support | Not every open model calls tools reliably; agents need it |
| Licensing | Open-weight ≠ unrestricted commercial use; read the model license |

### 7.2 Tools

| Tool | Use |
|---|---|
| [Ollama](https://docs.ollama.com/) | Easiest server; OpenAI- **and** Anthropic-compatible API |
| [LM Studio](https://lmstudio.ai/docs) | GUI + local server; used in the DeepLearning.AI cloud-to-local course |
| [llama.cpp](https://github.com/ggml-org/llama.cpp) | The engine under most local tools; max control |
| [vLLM](https://docs.vllm.ai) | High-throughput serving on a GPU server (team/production use) |

### 7.3 Local models inside your coding-agent team

- **Claude Code + local model:** [Ollama Anthropic compatibility](https://docs.ollama.com/api/anthropic-compatibility) (Ollama ≥ 0.14). Set `ANTHROPIC_BASE_URL=http://localhost:11434`, `ANTHROPIC_AUTH_TOKEN=ollama`, then `claude --model qwen3-coder` (or another pulled model). Raise context to ≥32K.
- **Codex / OpenAI-compatible tools:** point them at Ollama/LM Studio's OpenAI-compatible endpoint.
- **.NET apps:** `IChatClient` from Microsoft.Extensions.AI with the Ollama provider, so a config switch swaps local and cloud.
- **Realistic use:** local models do well on cheap roles (test scaffolding, search, summaries, private-code tasks). Keep a frontier model for planning and review. Measure this; don't assume it.
- Local inference ≠ private by default: plugins, telemetry and MCP servers can still send data out.

Phase C exercise: run the same Phase-B task with (a) Claude only, (b) Claude implementer + Codex reviewer, (c) local model as tester. Compare accepted-change time, defects, cost.

---

## 8. Building and adapting models (Q7)

Order matters. Each step has a concrete output.

| Step | Resource | Cost · time | Output |
|---|---|---|---|
| 1. How LLMs work, in code | [Neural Networks: Zero to Hero, Andrej Karpathy](https://karpathy.ai/zero-to-hero.html) | Free · 20–30 h | You write a GPT from scratch in PyTorch |
| 2. Transformers ecosystem | [Hugging Face LLM Course](https://huggingface.co/learn/llm-course/chapter1/1) | Free · 30–40 h | Tokenizers, datasets, fine-tuning with `transformers` |
| 3. Practical fine-tuning | [Unsloth docs](https://docs.unsloth.ai) (LoRA/QLoRA, fits a single consumer GPU or free Colab) + [Hugging Face TRL](https://huggingface.co/docs/trl) (SFT, DPO, GRPO) | Free · 10–15 h | A LoRA adapter on your own dataset, exported to GGUF and served by Ollama |
| 4. Post-training theory | [Post-training of LLMs (DeepLearning.AI)](https://www.deeplearning.ai/short-courses/post-training-of-llms/) | Free audit · ~2 h | When to use SFT vs DPO vs RL |
| 5. Full training run (optional) | [karpathy/nanochat](https://github.com/karpathy/nanochat) | ~$100 of rented GPU | Tokenizer → pretrain → SFT → chat UI, end to end |
| 6. Research depth (optional) | [Stanford CS336: Language Modeling from Scratch](https://cs336.stanford.edu/) | Free materials · 100+ h | Needs linear algebra, probability, PyTorch, GPU budget |

Before fine-tuning, always have (a) a prompt + RAG baseline and (b) a held-out eval set. Fine-tune only when it measurably beats the baseline. Fine-tuning appears in about 15% of AI-engineer postings, while agents/RAG/evals appear far more often (section 11). Treat this track as a long-term investment, not a job requirement.

Phase F exercise: fine-tune a 3–8B model with Unsloth on ~1,000 examples from your domain (e.g. your codebase's commit-message or API-doc style), serve it with Ollama, compare it with the base model on your eval set.

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
- [ ] Cross-model review set up (codex-plugin-cc or Agent HQ)
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
