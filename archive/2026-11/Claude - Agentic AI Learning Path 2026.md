# Agentic AI Learning Path 2026

Nov 2, 2026 · @Oleksii

## How to use this

Your goal has two steps. First, run Claude and Codex as a team of separate coding agents (planner, implementers, tester, reviewer) on your own repos. Then build agents yourself. Tier 1 plus the workflow section below cover step one in about 7 weeks. Tier 2 is step two. Tier 3 is credentials and models.

- **Assumes:** senior React/.NET/Python, comfortable with LLM basics (tokens, context windows, tool calling). Pure-beginner courses are left out.
- **Two stacks, one skill set:** Copilot + OpenAI models at work, Claude + Claude Code locally. The concepts that transfer — context engineering, instructions/skills/custom agents, MCP, evals — are prioritized over vendor trivia.
- **Free first:** 13 of 16 items are free. Paid: AI Evals (Maven), certification exam fees, some DeepLearning.AI Pro graded assignments.
- **Rule of thumb:** after every course, ship one small thing in your own repo. Courses without practice decay in weeks.

## Your target workflow: an AI dev team you run

The "independent AI engineers" setup is one pipeline of separate agent sessions, each with one role, its own context and its own branch. You stay architect and final reviewer. It runs on tools you already have, Claude Code and Codex, plus git worktrees and a few config files.

&#91;embedded content: multi-agent coding flow · 6 roles, 1 gate\]

Using a different model for review is deliberate: Codex reviewing Claude's code, or the reverse, catches mistakes a same-model reviewer tends to share.

| Layer | Claude side (local) | OpenAI / GitHub side (work) | What to know |
| --- | --- | --- | --- |
| Define roles once | [Subagent files](https://code.claude.com/docs/en/sub-agents) in .claude/agents (planner, tester, reviewer), skills, CLAUDE.md | AGENTS.md, custom agents in .github/agents, Copilot skills | A role = instructions + allowed tools + model. Keep each under one page |
| Run roles in parallel | Subagents (stable); [agent teams](https://code.claude.com/docs/en/agent-teams) (experimental: shared task list, teammates message each other); [git worktrees](https://code.claude.com/docs/en/worktrees) | Codex sub-agents; Copilot coding agent, one issue per PR | Start with 3–5 teammates; each must own different files or they overwrite each other |
| Cross-model review | [codex-plugin-cc](https://codex.danielvaughan.com/2026/03/31/codex-plugin-cc-cross-model-bridge/) (OpenAI): /codex:review, /codex:adversarial-review, optional review gate | [Agent HQ](https://www.i-programmer.info/news/105-artificial-intelligence/18651-github-adds-claude-and-codex-to-agent-hq.html): Claude and Codex as agents on GitHub issues/PRs (Copilot Pro+/Enterprise, preview) | Plugin uses your ChatGPT plan or API key; review loops can spike cost |
| Quality gates | Hooks: TaskCompleted, TeammateIdle (exit code 2 blocks and sends feedback) | GitHub Actions, branch protection, required reviews | Gates are code, not prompts — agents can't talk past a failing test |
| Manage many sessions | [Claude Squad](https://agentsroom.dev/blog/best-multi-agent-coding-tools) (tmux + worktree per agent), orchestrators such as oh-my-claudecode | GitHub mission control | This tool category churns fast; keep the layer thin and swappable |

**On Windows:** agent-team split panes need tmux or iTerm2 and don't work in Windows Terminal or the VS Code terminal. Use the default in-process mode, or run the setup in WSL2.

**Practice:** define planner, tester and reviewer subagents for one repo, install codex-plugin-cc, then take one real feature through the full loop in worktrees. Log tokens and wall-clock time per feature, so you know whether the team beats a single agent.

## Tier 1 — Single-agent mastery (weeks 1–2)

These four change your daily output immediately: the Explore → Plan → Code → Review loop, repo instruction files, custom agents/subagents, skills, and MCP — in both Claude Code and Copilot.

| # | Course | Provider · cost · time | What you get | Why for you |
| --- | --- | --- | --- | --- |
| 1 | [Claude Code in Action](https://academy.claude.com) + [Introduction to Subagents](https://academy.claude.com) + [Introduction to Agent Skills](https://academy.claude.com) | Claude Academy (formerly Anthropic Academy) · free · \~4–5 h | CLAUDE.md, hooks, subagents, reusable SKILL.md workflows | Your local stack; skills/subagents are now an open pattern Copilot also adopted |
| 2 | [Claude Code: A Highly Agentic Coding Assistant](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant) | DeepLearning.AI × Anthropic · free · \~2 h | Real projects: RAG codebase, refactors, Figma → web app, GitHub integration, MCP | Fast hands-on complement to #1 |
| 3 | [GitHub Copilot Fundamentals](https://learn.microsoft.com/en-us/training/paths/copilot/) + [Copilot coding agent module](https://learn.microsoft.com/en-us/training/modules/github-copilot-code-agent/7-summary) + [Configure Copilot instructions and create custom agents](https://learn.microsoft.com/en-us/training/modules/configure-customize-github-copilot-visual-studio-code/1-introduction) | Microsoft Learn · free · \~6–8 h | copilot-instructions.md, path instructions, .github/agents, assigning issues to the cloud agent; skills, MCP and CLI via the GH-300 study guide | Your work stack; makes your repos "agent-ready" for the whole team |
| 4 | [Codex Bootcamp 101/201/301](https://academy.openai.com/public/clubs/builders-etkn1/resources/codex-bootcamp-2026-09-23) | OpenAI Academy · free · live series ended Oct 7, 2026; watch recordings if posted | AGENTS.md, team workflows, sub-agents, permissions, Codex SDK | How OpenAI models behave as agents — useful since Copilot runs them |

**Optional credential:** GH-300 GitHub Copilot certification ([DataCamp prep track](https://www.datacamp.com/tracks/github-copilot-fundamentals-gh-300), updated June 2026, covers Agent Mode and MCP). Worth it only if your employer values it.

**Practice:** add a copilot-instructions.md and a CLAUDE.md/AGENTS.md to your main repo, write two skills (e.g., "add EF Core migration", "React component + tests"), and assign one real issue to the Copilot coding agent.

## Tier 2 — Building agents, your next step (months 3–5)

The core of the path: agent patterns, MCP, one framework per stack, and evals — the skill that separates demos from production.

| # | Course | Provider · cost · time | What you get | Why for you |
| --- | --- | --- | --- | --- |
| 5 | [Building with the Claude API](https://academy.claude.com) | Claude Academy · free · \~8 h | Tool use, RAG, structured outputs, prompt caching, agent architecture | First-principles agent loop without a framework |
| 6 | [Introduction to MCP](https://academy.claude.com) + [MCP Advanced Topics](https://academy.claude.com) | Claude Academy · free · \~4 h | Tools/resources/prompts, transports, Inspector, production patterns | MCP is the shared plug-in layer for Claude Code, Copilot and Codex — build one server, use it everywhere |
| 7 | [Agentic AI](https://www.deeplearning.ai/courses/agentic-ai/) | DeepLearning.AI (Andrew Ng) on Coursera · graded work paid · \~20 h | Reflection, tool use, planning, multi-agent, error analysis | Vendor-neutral mental model in raw Python |
| 8 | [Develop AI agents on Azure](https://learn.microsoft.com/en-us/training/paths/develop-ai-agents-on-azure/) + [Generative AI for Beginners .NET](https://github.com/microsoft/generative-ai-for-beginners-dotnet) | Microsoft · free · \~10 h | Microsoft Agent Framework 1.0 (successor to Semantic Kernel/AutoGen), Microsoft.Extensions.AI, vector data, Foundry | **Your highest-leverage build skill** — agents in your existing .NET backends |
| 9 | [LangGraph Essentials + LangChain Essentials](https://academy.langchain.com) | LangChain Academy · free · \~6–8 h | Stateful graphs, memory, human-in-the-loop, durable execution | Most-used Python orchestration framework; Python and TS versions. Skip the older Intro course, which predates LangGraph v1 |
| 10 | [Builder Bootcamp: Agents, RAG, Production](https://academy.openai.com/en/public/clubs/builders-etkn1/overview) | OpenAI Academy · free · live series ended Sep 2026; pair with the Agents SDK docs | OpenAI Agents SDK, RAG, evals, optimization | Same concepts as #5 on the models you use at work |
| 11 | [AI Evals for Engineers & PMs](https://maven.com/parlance-labs/evals) | Hamel Husain & Shreya Shankar (Maven) · $4,400 · cohort, next run Jan 30–Mar 27, 2027 | Error analysis, LLM-as-judge, trace review, CI evals for agents | The scarcest production skill; \~5k engineers trained. Budget alternative: their book *Evals for AI Engineers* (O'Reilly, Oct 2026) |

**Pick, don't stack:** do #8 (.NET) and #9 *or* #10 (Python) — not every framework. Patterns transfer; APIs churn.

**GCP work:** if agent projects land on GCP, Google's [Agent Development Kit (ADK)](https://docs.cloud.google.com/agent-builder/agent-development-kit/overview) on Vertex AI plays the same role as Agent Framework does on Azure. Learn it only when a project needs it; the patterns from #7–#9 carry over.

**Practice:** build one MCP server in .NET exposing an internal API, then a Microsoft Agent Framework agent that uses it, with a 30-case eval set run in CI.

## Tier 3 — Credentials and models (later; OWASP moves to week 6)

Security is mandatory; one certification is optional; model training is a later, separate track.

| # | Item | Provider · cost · time | What you get | Why for you |
| --- | --- | --- | --- | --- |
| 12 | [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org) + OWASP Top 10 for LLM Applications 2026 (published Aug 2026) | OWASP · free · \~3 h reading | Goal hijack, tool misuse, memory poisoning, "least agency"; prompt injection still #1 | Agents with tool access are a new attack surface in your backends |
| 13 | [Claude Certified Architect – Foundations (CCA-F)](https://anthropic.skilljar.com/claude-certified-architect-foundations-access-request) | Anthropic · $125 exam via Partner Academy · 60–120 h prep | Proctored, 60 questions/120 min, pass 720/1000; agentic architecture 27%, Claude Code config 20%, prompting 20%, MCP 18%, context mgmt | Validates Tier 1–2 Claude work; recommends \~6 months hands-on first |
| 14 | [AI-103: Developing AI Apps and Agents on Azure](https://learn.microsoft.com/en-us/credentials/certifications/) | Microsoft · exam, still in beta | Foundry, RAG, agents (agents now 30–35% of the exam) | Replaced AI-102 (retired June 30, 2026). Pick this *or* #13, based on your employer's cloud. For you that's Azure, so take this one; CCA-F stays optional |
| 15 | [Hugging Face Agents Course](https://huggingface.co/learn/agents-course) + [LLM Course](https://huggingface.co/learn/llm-course) | Hugging Face · free · \~40–50 h | Transformers, tokenizers, PEFT/LoRA fine-tuning, function-calling fine-tune, agent benchmarks | Your "build models in the future" goal starts here |
| 16 | Fine-tuning & RL for LLMs: Intro to Post-training | DeepLearning.AI · certificate needs Pro · \~13 h | SFT, DPO, RL-based post-training | Only after #15, and only if you'll actually fine-tune |

**Honest note on "building models":** for most product teams in 2026, value comes from agents, context and evals on frontier models, not training. Treat #15–16 as a long-term track.

## Independent validation

A separate agent re-checked every item against fresh sources: 12 of 16 hold up as current and in demand, LangGraph's intro course is replaced, and four gaps were added. Corrections it found are already applied in the tables above.

| # | Item | Current? | 2026 demand | Verdict |
| --- | --- | --- | --- | --- |
| 1 | Claude Academy: Claude Code, Subagents, Skills | Yes | High | Keep |
| 2 | DL.AI Claude Code course | Yes, updated Aug 2026 | High | Optional, overlaps #1 |
| 3 | Copilot on Microsoft Learn / GH-300 | Yes, GH-300 updated Aug 2026 with agent mode, MCP, CLI | High | Keep; certify only if valued |
| 4 | Codex Bootcamp | Live series, already over | Medium | Optional via recordings |
| 5 | Claude API course | Yes | High | Keep |
| 6 | MCP intro + advanced | Yes | High | Keep |
| 7 | DL.AI Agentic AI | Yes, updated Sep 2026 | High | Keep |
| 8 | Microsoft Agent Framework .NET | Yes, GA Apr 2026; path updated Oct 28, 2026 | High | Keep, your top differentiator |
| 9 | LangGraph | Intro course outdated vs v1 | Medium–High | Replaced with Essentials |
| 10 | OpenAI Builder Bootcamp | Live series, already over | Medium | Optional; use SDK docs |
| 11 | AI Evals (Maven) | Yes | High | Keep; ask employer to fund |
| 12 | OWASP agentic + LLM Top 10 | Yes | High | Keep |
| 13 | CCA-F | Yes, launched Mar 2026 | Low–Medium | Optional |
| 14 | AI-103 | Beta; AI-102 retired | Medium | Optional, after GA |
| 15 | Hugging Face Agents + LLM | Partly verified | Low–Medium | Later |
| 16 | DL.AI post-training | Yes, updated Jul 2026 | Low–Medium | Pick #15 or #16 |

**What employers ask for in 2026** (validator's sources):

- Across 3,449 AI Engineer postings (May 2026): Python 71%, LLMs 66%, RAG 40%, Azure 33%, monitoring 31%, LangChain 25%.
- LangChain's State of Agent Engineering survey (n=1,340): 57% have agents in production, 89% use observability, 52% run offline evals.
- 57% of teams don't fine-tune; fine-tuning appears in \~15% of postings. No evidence employers ask for CCA-F, AI-103 or GH-300 yet.

**Added after validation:**

- [ ] **Claude Agent SDK** — Anthropic's own agent harness; the list covered OpenAI's and Codex's SDKs but not this one. Start with the official docs.
- [ ] **Agent observability** — OpenTelemetry tracing (plus Langfuse or LangSmith); Aspire already gives you this in .NET.
- [ ] **A2A protocol** — agent-to-agent interop; now its own module in the Microsoft agents path.
- [ ] **OWASP Agent Control Standard (v0.1)** — runtime guardrails and an agent bill of materials; watch, don't deploy yet.

### Re-validation for your clarified goal

Operating multi-agent coding comes first and building agents second, so seven items move later, one moves earlier, and four workflow items are added. None are dropped.

| Item | Before | Now | Why |
| --- | --- | --- | --- |
| #1 Claude Academy (Code, Subagents, Skills) | Tier 1 | Core, weeks 1–2 | Subagent and skill files are how you define roles |
| #2 DL.AI Claude Code | Optional | Core, week 2 | Covers worktrees and parallel sessions hands-on |
| #3 Copilot + coding agent | Tier 1 | Core, weeks 1–2 | Your work-side implementer; Agent HQ adds Claude and Codex as GitHub agents |
| #4 Codex Bootcamp | Optional | **Upgraded**, week 3 | Codex is your reviewer role. Use recordings or the Codex docs |
| #12 OWASP agentic Top 10 | Tier 3 | **Moved up**, week 6 | Agents with shell and git access on your machine are the main risk now |
| #5–#11 build courses | Tier 2, weeks 4–9 | Next step, months 3–5 | Needed to build agents, not to run them |
| #13–#14 certifications | Tier 3 | After step two | No employer-demand evidence yet |
| #15–#16 models | Tier 3 | Later, optional | Unchanged |

**Added for the workflow:**

- [ ] [Claude Code docs](https://code.claude.com/docs/en/agent-teams) on subagents, agent teams, worktrees and hooks (free, official, current). This is the single most useful read for your goal.
- [ ] [codex-plugin-cc](https://codex.danielvaughan.com/2026/03/31/codex-plugin-cc-cross-model-bridge/), OpenAI's official plugin that runs Codex reviews inside Claude Code.
- [ ] GitHub Agent HQ, which runs Claude and Codex agents on issues and PRs at work, if your plan is Pro+ or Enterprise.
- [ ] One session manager, such as Claude Squad in WSL2. Treat it as optional; native agent teams may be enough.

## Suggested schedule and capstones

At \~8–10 hours a week, the workflow phases take about 7 weeks; building agents starts in month 3. Every capstone builds on the previous one, so you have a working AI dev team on your own repo before you start building agents yourself.

&#91;embedded content: 12-week plan · 4 phases + optional model track\]

Phases 1–3 are your current goal. Phase 4 is the next step you described, reusing the same repo, roles and gates.

## Sources

- [Claude Academy relaunch (academy.claude.com, 26 courses, Aug 2026)](https://pasqualepillitteri.it/en/news/12081/claude-academy-live-free-courses-learn-claude)
- [Anthropic Academy course list, April 2026 update](https://pasqualepillitteri.it/en/news/371/anthropic-academy-free-courses-claude)
- [DeepLearning.AI: Claude Code: A Highly Agentic Coding Assistant](https://www.deeplearning.ai/short-courses/claude-code-a-highly-agentic-coding-assistant)
- [DeepLearning.AI Agentic AI (course review)](https://www.beri.net/learning/dlai-agentic-ai)
- [Microsoft Learn: Configure Copilot instructions and create custom agents](https://learn.microsoft.com/en-us/training/modules/configure-customize-github-copilot-visual-studio-code/1-introduction)
- [Microsoft Learn: Copilot coding agent](https://learn.microsoft.com/en-us/training/modules/github-copilot-code-agent/7-summary)
- [GitHub Copilot custom agents and skills guide](https://agentpatterns.ai/tools/copilot/custom-agents-skills/)
- [DataCamp GH-300 track](https://www.datacamp.com/tracks/github-copilot-fundamentals-gh-300)
- [OpenAI Academy Codex Bootcamp](https://academy.openai.com/public/clubs/builders-etkn1/resources/codex-bootcamp-2026-09-23)
- [OpenAI Academy Builders community](https://academy.openai.com/en/public/clubs/builders-etkn1/overview)
- [Generative AI for Beginners .NET (MAF 1.0 samples)](https://github.com/microsoft/generative-ai-for-beginners-dotnet)
- [Microsoft Agent Framework learning resources](https://microsoft.com/en-us/thesource-developer/Category/22/learn)
- [Best LangChain courses 2026](https://aiagentrank.io/blog/best-langchain-courses-2026)
- [AI Evals for Engineers & PMs (Maven)](https://maven.com/parlance-labs/evals?promoCode=FAST25)
- [Evals for AI Engineers (O'Reilly)](https://www.oreilly.com/library/view/evals-for-ai/9798341660717/ch02.html)
- [CCA-F exam guide (domains, scoring)](https://digitalstrategy-ai.com/claude-certified-architect-guide)
- [AI-102 vs AI-103 (2026)](https://open-exam-prep.com/blog/ai-102-vs-ai-103-which-exam-2026)
- [OWASP Top 10 for Agentic Applications 2026 (Aikido summary)](https://aikido.dev/blog/owasp-top-10-agentic-applications)
- [OWASP LLM Top 10 2026 (SD Times)](https://sdtimes.com/security/prompt-injection-tops-2026-owasp-genai-llm-top-ten-vulnerabilities/)
- [Hugging Face Agents Course](https://huggingface.co/learn/agents-course)
- [Hugging Face LLM Course review](https://aiagentrank.io/learn/course/huggingface-llm-course)

Added for the multi-agent workflow (Nov 2, 2026):

- [Claude Code docs: agent teams (experimental)](https://code.claude.com/docs/en/agent-teams)
- [codex-plugin-cc: Codex inside Claude Code](https://codex.danielvaughan.com/2026/03/31/codex-plugin-cc-cross-model-bridge/)
- [GitHub adds Claude and Codex to Agent HQ (Feb 2026)](https://www.i-programmer.info/news/105-artificial-intelligence/18651-github-adds-claude-and-codex-to-agent-hq.html)
- [Tools to run multiple coding agents in 2026 (vendor comparison)](https://agentsroom.dev/blog/best-multi-agent-coding-tools)
