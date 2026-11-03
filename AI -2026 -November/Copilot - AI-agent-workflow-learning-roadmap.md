# Coding Agent Workflows: A Practical Learning Roadmap

Prepared: November 2, 2026  
Audience: Senior software developer with 10+ years of commercial experience in React, .NET, and Python  
Primary goal: Continue growing as a software developer by reliably directing coding agents  
Secondary goal: Build custom agent orchestration, then explore local inference and model adaptation

> Learn to supervise one coding agent, delegate bounded work, coordinate isolated sessions, and only then automate the coordination. Model training is a separate specialization, not a prerequisite.

## Contents

- [1. Scope and priorities](#1-scope-and-priorities)
- [2. Essential concepts](#2-essential-concepts)
- [3. How a practical coding-agent workflow works](#3-how-a-practical-coding-agent-workflow-works)
- [4. Tooling and local setup](#4-tooling-and-local-setup)
- [5. Prioritized courses and documentation](#5-prioritized-courses-and-documentation)
- [6. Six-to-eight-week practical plan](#6-six-to-eight-week-practical-plan)
- [7. Reusable task and handoff templates](#7-reusable-task-and-handoff-templates)
- [8. Quality, security, and autonomy boundaries](#8-quality-security-and-autonomy-boundaries)
- [9. Measuring and improving the workflow](#9-measuring-and-improving-the-workflow)
- [10. Next stage: building your own orchestration](#10-next-stage-building-your-own-orchestration)
- [11. Later tracks: AI applications, local models, and model training](#11-later-tracks-ai-applications-local-models-and-model-training)
- [12. Demand evidence and independent validation](#12-demand-evidence-and-independent-validation)
- [13. Completion checklist](#13-completion-checklist)
- [14. Keeping the roadmap current](#14-keeping-the-roadmap-current)

## 1. Scope and priorities

Your near-term objective is not to become a machine-learning researcher or replace your existing engineering stack. It is to use coding agents as a reliable extension of your software-development practice.

The immediate learning priorities are:

1. Define work precisely enough that an agent can complete and verify it.
2. Configure repository context, instructions, tools, and permissions.
3. Use specialist workers for implementation, test design, review, and investigation.
4. Isolate concurrent changes and integrate them deliberately.
5. Evaluate actual outcomes rather than trusting agent summaries.
6. Automate only workflows that already work under supervision.

The earlier, broader curriculum included RAG, orchestration frameworks, and deep learning. These remain useful later, but they are not prerequisites for operating a productive team of coding agents.

### Three different learning tracks

| Track | Main question | Priority |
|---|---|---|
| Agent-assisted software engineering | How do I use agents to implement, test, review, and maintain ordinary software? | Now |
| Agent application and orchestration engineering | How do I build software that launches agents, connects tools, manages state, and enforces boundaries? | Next |
| Model engineering | How do I run, adapt, evaluate, or train model weights? | Later, if needed |

## 2. Essential concepts

| Concept | Meaning | Practical implication |
|---|---|---|
| Model | The inference system producing responses and tool-call requests. | Model choice affects quality, latency, context limits, and cost. |
| Coding harness | Software around the model that provides editing, terminal tools, context management, permissions, and sessions. Examples include Claude Code, Codex, and Copilot. | Changing the harness can change behavior even when model capability is similar. |
| Agent | A system that uses a model and tools iteratively to pursue a task. | Useful autonomy depends on the environment and controls, not just prompting. |
| Custom agent/profile | A reusable specialist definition: instructions, role, and supported tool configuration. | A tester or reviewer is usually a configured role, not a separately trained model. |
| Subagent | A delegated worker operating in a separate context. | Good for bounded work that would otherwise clutter the main conversation. |
| Independent session | A separate coding-agent session with its own conversation and task. | Useful for separate feature branches, worktrees, or cross-tool review. |
| Agent team | A coordinated group of sessions with task-sharing or messaging features. | Adds coordination overhead and may depend on experimental capabilities. |
| Skill | Reusable procedural instructions and supporting resources. | Useful for repeated tasks such as adding an endpoint or checking a migration. |
| Hook | Code run at defined lifecycle events. | Can enforce deterministic checks where the harness supports appropriate blocking behavior. |
| MCP | Model Context Protocol: a standard for exposing tools and resources to clients. | Tool connectivity is not the same as orchestration, authorization, or safety. |
| Worktree | A separate working directory attached to the same Git repository. | Isolates file and branch state, not processes, credentials, networks, or databases. |
| Evaluation | A repeatable way to measure whether a configuration performs acceptably. | For coding, start with behavior, tests, accepted changes, review effort, and regressions. |

### Local application versus local inference

Running Claude, Codex, or another coding client on your computer does not necessarily mean model inference happens locally.

- **Local harness, hosted inference:** Files and commands may be handled locally while model requests go to a provider.
- **Local harness, local inference:** Model weights and inference run on your hardware.
- **Hosted agent execution:** The provider runs the agent's execution environment remotely.

You do not need a GPU to begin with a local coding harness that uses hosted inference. A local desktop client is also not a guarantee that your code stays on your device.

## 3. How a practical coding-agent workflow works

```text
You: requirements, constraints, acceptance criteria
                         |
                Coordinator / planner
                         |
             Agree on contracts and boundaries
                         |
          +--------------+---------------+
          |                              |
   Frontend implementer           Backend implementer
   Separate worktree              Separate worktree
          |                              |
          +--------------+---------------+
                         |
              Integrate a candidate change
                         |
          Test execution + independent review
                         |
                 Fix accepted findings
                         |
           CI gates + human approval to merge
```

Not every box needs a separate agent. Start with one implementer and one reviewer.

### Recommended initial roles

| Role | Responsibility | Required output |
|---|---|---|
| You / technical lead | Resolve requirements, approve important decisions, assess risk, control merge and deployment. | Acceptance criteria and decisions. |
| Implementer | Make a bounded change following existing patterns. | Diff or commit, explanation, executed checks, remaining uncertainties. |
| Test-focused worker | Derive tests from requirements and failure modes; identify missing coverage. | Test cases, results, and gaps. |
| Reviewer | Inspect the actual candidate change independently. | Actionable findings with file references, behavior, and evidence. |
| Coordinator, when useful | Track dependencies and handoffs; collect results. | Task state and integration readiness. |

A coordinator does not need to be an agent initially. You can perform that role manually.

### When parallel work is useful

- Frontend and backend work after agreeing on an API contract.
- Independent repository exploration or competing debugging hypotheses.
- Read-only review of separate concerns.
- Separate modules with explicit ownership.

### When parallel work is usually counterproductive

- Several workers editing the same files or tightly coupled business logic.
- Small tasks that one agent could complete quickly.
- Work with unresolved requirements.
- A sequence where each step depends on the previous step's output.
- Workers sharing mutable test data, ports, or generated output without isolation.

### Mixing Claude Code and Codex

A useful first cross-tool workflow is:

1. Claude Code implements a bounded change.
2. Record the exact commit or candidate diff.
3. Codex reviews that candidate in a separate context.
4. You assess the findings rather than applying every suggestion automatically.
5. The implementer fixes accepted issues.
6. Tests and CI check the final integrated candidate.

The tools can be reversed. There is no universal rule that one provider should implement and another should review.

Installing both tools does not create automatic cross-provider task coordination. Start with explicit handoffs; add scripting later. Agreement between models is not proof of correctness.

## 4. Tooling and local setup

### Minimum viable stack

| Component | Recommendation |
|---|---|
| Primary harness | Choose Claude Code, Codex, or your existing Copilot environment. |
| Repository isolation | Git branches and worktrees for concurrent editing sessions. |
| Verification | Existing .NET/frontend tests, type checking, linting, browser tests, and CI. |
| Repository guidance | Concise architecture notes, exact build/test commands, conventions, and boundaries. |
| Specialist configuration | A small number of reusable roles; start with implementer and reviewer. |
| Permission controls | Restrict writable paths, commands, network access, and credentials where supported. |
| Secondary harness | Optional at first; useful for independent review and comparisons. |
| Logs and task state | Record assignments, candidate commits, checks, findings, and unresolved blockers. |

Use the repository's existing test frameworks. Do not introduce a new test stack merely because an agent prefers one.

### Windows considerations

Windows-native execution is a valid starting point; WSL is not inherently required for the entire workflow. Choose the environment based on current tool support and your project's dependencies.

- Use worktrees with Windows paths and a consistent shell.
- Check that each harness can find the required .NET, Node.js, Python, and Git installations.
- Isolate development-server ports and test databases when sessions run concurrently.
- Avoid sharing mutable generated output across worktrees.
- Check current sandbox support rather than assuming a prompt creates isolation.

Resources:

- [Codex on Windows and sandbox options](https://developers.openai.com/codex/windows)
- [Claude Code common workflows, including worktrees](https://code.claude.com/docs/en/common-workflows)
- [Git worktree reference](https://git-scm.com/docs/git-worktree)

### Repository instruction files

Use the instruction mechanisms supported by your actual client. Common examples include `CLAUDE.md`, `AGENTS.md`, and `.github\copilot-instructions.md`.

Include:

- Exact setup, build, targeted-test, and full-check commands.
- Architecture and component boundaries.
- Coding conventions that are not obvious from nearby examples.
- Rules for migrations, generated files, public APIs, and dependencies.
- Explicit limits on secrets, external services, deployment, and destructive operations.

Keep shared facts consistent across tool-specific files. File recognition, precedence, inheritance, and subagent behavior vary by client; do not assume every tool loads every file.

### Authentication, cost, and work policy

Confirm which subscriptions or API credentials each workflow requires. Interactive subscriptions, API access, SDK use, and CI authentication are not interchangeable entitlements.

Keep personal and work access separate. Only use work code with providers and integrations approved by your organization. Do not expose production credentials to a coding agent simply to make a task easier.

## 5. Prioritized courses and documentation

This sequence is optimized for operating coding agents, not for building an AI product immediately.

Effort estimates below are planning estimates including exercises, not provider-promised durations. You can skip material when you already demonstrate the outcome.

### 5.1 Claude Code in Action

**Resource:** [Claude Code in Action - Anthropic](https://anthropic.skilljar.com/claude-code-in-action)

**Priority:** Essential if Claude Code is your primary personal tool.  
**Estimated practice-inclusive effort:** 6-10 hours.

Focus on planning, context compaction, repository instructions, skills, hooks, permissions, unattended execution, and verification.

**Exercise:** Complete a bounded feature with an explicit plan and real checks. Identify which controls are instructions and which are enforced by the harness.

**Completion outcome:** You can steer a longer task, recover from a wrong direction, and assess the result without trusting the final summary alone.

### 5.2 Specialist agents in your primary harness

**Resources:**

- [Claude Code custom subagents](https://code.claude.com/docs/en/sub-agents)
- [Codex subagent workflows](https://developers.openai.com/codex/multi-agent)
- [GitHub Copilot custom agents](https://docs.github.com/en/copilot/concepts/customizations/custom-agents)
- [GitHub Copilot best practices for delegated tasks](https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results)

**Priority:** Essential. Learn one environment first.  
**Estimated practice-inclusive effort:** 4-8 hours.

Learn what context workers receive, how roles are selected, what permissions they inherit, how results return, and what parallelism costs.

**Exercise:** Define implementer, test-focused, and reviewer roles. Use only the roles needed for a particular task.

**Completion outcome:** You can explain the difference between selecting a profile, delegating to a subagent, and starting an independent session.

### 5.3 Worktrees, parallel sessions, and integration

**Resources:**

- [Claude Code common workflows](https://code.claude.com/docs/en/common-workflows)
- [Git worktree documentation](https://git-scm.com/docs/git-worktree)
- [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams)

**Priority:** Essential for concurrent editing; agent teams are optional.  
**Estimated practice-inclusive effort:** 4-8 hours.

Learn workspace ownership, branch boundaries, shared-resource conflicts, contract-first decomposition, and integration checks.

**Current caveat:** The Claude Code agent-team documentation reviewed for this guide labels the feature experimental and disabled by default. It is not required for a useful multi-agent workflow.

**Exercise:** Implement a small React/.NET feature in two worktrees after agreeing on request/response types and error behavior.

**Completion outcome:** Both changes work together, not just independently.

### 5.4 AI Coding Workflows: From Cloud to Local

**Resource:** [DeepLearning.AI + JetBrains course](https://www.deeplearning.ai/courses/ai-coding-workflows-from-cloud-to-local/)

**Priority:** Strongly recommended for your personal/work tool split.  
**Provider-listed video duration:** 1 hour 32 minutes.  
**Page-listed update:** September 18, 2026.  
**Estimated practice-inclusive effort:** 4-8 hours.

The course compares harnesses, focused subagents, smaller models, providers, and cloud/hybrid/local execution. Its local-inference examples use LM Studio.

**Exercise:** Compare two configurations on the same small task and acceptance criteria. Record quality, elapsed time, intervention, and cost.

**Completion outcome:** You can choose a setup based on evidence rather than model rankings or tool marketing.

### 5.5 Context engineering

**Resource:** [Effective context engineering for AI agents - Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

**Priority:** Essential reading.  
**Estimated practice-inclusive effort:** 2-4 hours.

Learn focused context, just-in-time retrieval, compact handoffs, persistent decisions, and avoiding context pollution.

**Exercise:** Give a reviewer requirements and the candidate diff without the implementer's persuasive narrative. Compare the usefulness of its findings.

**Completion outcome:** Each worker receives enough context to succeed without inheriting irrelevant history.

### 5.6 Evaluation and failure analysis

**Resource:** [Evaluating AI Agents - DeepLearning.AI + Arize](https://www.deeplearning.ai/short-courses/evaluating-ai-agents/)

**Priority:** Recommended; adapt the material to coding outcomes.  
**Estimated practice-inclusive effort:** 4-8 hours.

The course covers traces, component and end-to-end evaluation, code-based checks, human annotations, LLM judges, and structured experiments.

For coding workflows, begin with existing engineering checks and a task scorecard. You do not need an elaborate evaluation platform before your first useful experiment.

**Exercise:** Compare a single-agent baseline with a delegated workflow across several comparable tasks. Label failures and false-positive review findings.

**Completion outcome:** You can identify whether delegation improves accepted outcomes enough to justify its coordination cost.

### 5.7 Programmatic execution

**Resources:**

- [Run Claude Code programmatically](https://code.claude.com/docs/en/headless)
- [Codex non-interactive execution](https://developers.openai.com/codex/noninteractive)
- [Claude Agent SDK overview](https://code.claude.com/docs/en/agent-sdk/overview)

**Priority:** After manual handoffs are reliable.  
**Estimated practice-inclusive effort:** 6-12 hours.

Learn structured output, process exit status, session identifiers, permissions, timeouts, authentication, and error handling.

**Exercise:** Automate one bounded implement-review-check sequence. Make failed commands, unavailable tools, and incomplete work visible.

**Completion outcome:** A script can coordinate a known workflow without silently treating an incomplete run as success.

### Access and pricing notes

- Official documentation is freely readable; tool usage and services may cost money.
- Anthropic's academy offers free education; model/API usage is separate.
- DeepLearning.AI access varies by course. Assessments, projects, and certificates may require Pro.
- Check current enrollment terms rather than assuming a course's free preview includes all labs.
- API use, subscriptions, hosting, and GPU costs are separate budget items.
- Certificates are optional; demonstrated engineering outcomes are the priority.

## 6. Six-to-eight-week practical plan

Target approximately 5-7 hours per week. This is a starter plan, not a promise of mastery or a requirement to finish every optional exercise.

| Stage | Focus | Deliverable |
|---|---|---|
| Week 1 | Primary harness, permissions, repository instructions, planning. | One small feature completed with documented acceptance criteria and checks. |
| Week 2 | Specialist roles and context boundaries. | A test-focused assignment and an independent review with actionable findings. |
| Week 3 | Worktrees and parallel sessions. | Two isolated tasks integrated into one working change. |
| Week 4 | Contract-first React/.NET feature. | Agreed API contract, implementation, and integration evidence. |
| Week 5 | Cross-tool review and context engineering. | Review of an exact candidate commit; accepted and rejected findings recorded. |
| Week 6 | Measurement and refinement. | Baseline-versus-delegated comparison and a short failure taxonomy. |
| Weeks 7-8 | Optional programmatic orchestration and harder cases. | One bounded automated handoff with timeouts, visible errors, and approval boundaries. |

If worktree integration or review quality remains unreliable, repeat those stages instead of adding more agents.

### Suggested practice feature

Choose a small end-to-end feature with:

- A React form or list.
- A .NET endpoint.
- An agreed request/response contract and error cases.
- Existing or clearly defined test commands.
- No production credentials or high-risk migration.

Examples include adding a filter, a small validation rule, or a read-only detail view. Avoid starting with authentication redesign, a broad architecture migration, or a production incident.

## 7. Reusable task and handoff templates

### Implementation assignment

```text
Goal:
Implement [specific behavior].

Acceptance criteria:
- [Observable behavior]
- [Error or edge case]
- [Compatibility requirement]

Scope:
- Own [component/files].
- Do not modify [excluded areas].
- Coordinate before changing [shared contract].

Constraints:
- Follow existing patterns.
- Do not add dependencies without justification.
- Do not weaken existing tests.
- Stop for unresolved product or architecture decisions.

Verification:
- Run [specific checks].
- Report checks not run and why.

Return:
- Candidate commit/diff.
- Summary of behavior changed.
- Commands and results.
- Remaining risks, blockers, and assumptions.
```

### Independent reviewer assignment

```text
Review candidate [commit/diff] against these acceptance criteria: [criteria].

Inspect the actual implementation and relevant existing behavior.
Prioritize concrete correctness, compatibility, and regression issues.
Do not assume the implementer's summary proves correctness.
Do not edit files.

For each finding, return:
- File and location.
- Triggering condition.
- Incorrect behavior and impact.
- Evidence or reproduction.

Separate confirmed findings from uncertainties.
Return no findings if there is no sufficiently supported issue.
```

Enforce read-only behavior through supported controls where possible. The text instruction alone does not provide isolation.

### Test-focused assignment

```text
Derive tests from the requirements, public behavior, and failure modes.
Inspect existing test conventions before adding anything.

Cover:
- Normal behavior.
- Boundaries and invalid inputs.
- Relevant permissions and compatibility.
- Integration behavior where applicable.

Do not copy the implementation's assumptions into every assertion.
Do not remove or weaken assertions merely to obtain a passing run.
Report missing coverage and environmental limitations explicitly.
```

### Handoff record

```text
Task:
Owner:
Base commit:
Candidate commit:
Scope changed:
Acceptance criteria addressed:
Checks executed:
Checks not executed:
Dependencies or contract changes:
Known issues:
Decision needed:
```

A handoff should identify the exact candidate being reviewed. If that candidate changes, previous test and review evidence may no longer cover it.

## 8. Quality, security, and autonomy boundaries

### Verification hierarchy

Use several forms of evidence:

1. Requirements and observable acceptance criteria.
2. Existing tests plus deliberately chosen new cases.
3. Build, type, lint, and integration checks relevant to the change.
4. Independent inspection of the actual diff.
5. Human judgment for important product, architecture, and risk decisions.

Tests can be incomplete or wrong. Agent agreement can also be wrong. Neither should be the sole basis for accepting a critical change.

### Practical controls

- Keep work code within approved providers and tools.
- Use least-privilege credentials and tools.
- Keep secrets out of prompts, logs, commits, and broadly inherited process environments.
- Treat repository content, retrieved documents, issues, and tool output as untrusted inputs.
- Gate deployment, privileged actions, destructive operations, and sensitive changes.
- Restrict reviewer write access where supported.
- Keep retries and repair loops bounded.
- Make failure, missing verification, and approval requests explicit.
- Distinguish tool/process success from completion of the engineering task.
- Confirm what a harness loads before scripted execution, especially hooks and integrations from an untrusted repository.

### Suggested autonomy progression

| Level | Allowed work | Gate |
|---|---|---|
| Read-only | Exploration, planning, review. | Inspect findings and unsupported assumptions. |
| Bounded edits | Small changes in a controlled workspace. | Review diff and checks. |
| Delegated work | Specialist tasks with explicit ownership. | Verify handoffs and integrated result. |
| Automated sequence | Known, repeatable implement/review/check flow. | Stop on uncertainty, failure, or exhausted budget. |
| Privileged operations | Deployment, sensitive data changes, destructive actions. | Explicit authorization and stronger operational controls. |

Useful later security resources:

- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/)
- [Red Teaming LLM Applications - DeepLearning.AI](https://www.deeplearning.ai/short-courses/red-teaming-llm-applications/)

The red-teaming course is an introductory foundation, not complete coverage of agent authorization and execution security.

## 9. Measuring and improving the workflow

Measure useful engineering outcomes, not the number of agents or lines generated.

| Metric | What to record |
|---|---|
| Time to accepted change | Include planning, agent runtime, review, repair, and integration. |
| Human intervention | Time spent correcting instructions, resolving confusion, and repairing output. |
| Correctness | Acceptance criteria met, failures, regressions, and escaped defects. |
| Review quality | Useful findings, false positives, and missed issues found later. |
| Integration cost | Conflicts, contract mismatches, duplicated work, and shared-resource interference. |
| Cost per completed task | Include failed runs, subagents, retries, and secondary reviews. |
| Reliability | Completion rate, environmental failures, and recovery behavior. |

### Improvement loop

1. Establish a single-agent baseline.
2. Classify a failure: unclear requirements, missing context, incorrect tool use, implementation error, weak tests, or integration failure.
3. Change one relevant variable.
4. Repeat on comparable tasks, allowing for task variation and nondeterminism.
5. Keep the change only if outcomes improve enough to justify added complexity.

Possible improvements include clearer contracts, better repository navigation, narrower tools, stronger acceptance criteria, better test feedback, or a different model. Adding another agent is only one option.

Avoid defaulting to a fixed model/provider role split. Compare performance on your own React/.NET/Python tasks.

## 10. Next stage: building your own orchestration

The first custom system should coordinate existing coding harnesses rather than recreate every capability from scratch.

### A useful initial coordinator

```text
Task intake
   -> Validate scope and permissions
   -> Create isolated workspace
   -> Launch worker
   -> Collect structured result and exact candidate
   -> Run checks
   -> Request independent review
   -> Allow bounded repairs
   -> Stop for human approval
```

A Python or TypeScript implementation can manage subprocesses or use supported SDKs. Your .NET background remains useful for application integration; there is no need to change your production backend solely to learn orchestration.

### Responsibilities your coordinator must own

- Task IDs, state, dependencies, and ownership.
- Workspace creation and targeted cleanup.
- Structured input and output contracts.
- Exit codes, incomplete results, timeouts, and cancellation.
- Retry limits and protection against repeated side effects.
- Candidate commit identity and review/check invalidation after edits.
- Permissions, approvals, credential handling, and logs.
- Cost and concurrency limits.
- Escalation when the task cannot be completed confidently.

### Starting resources

- [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview)
- [Claude programmatic execution](https://code.claude.com/docs/en/headless)
- [Codex non-interactive execution](https://developers.openai.com/codex/noninteractive)
- [OpenAI agent runtime options](https://developers.openai.com/api/docs/guides/agents)

The current OpenAI documentation distinguishes managed Agents API, Agents SDK, and Responses API approaches. They differ in who runs the loop, stores state, executes tools, and handles infrastructure.

For SDK and automation use, verify authentication and commercial terms separately from interactive subscription access.

### When MCP becomes useful

Learn MCP when you need reusable connections to tools or data, such as a controlled issue tracker or internal documentation service.

- [Hugging Face MCP Course](https://huggingface.co/learn/mcp-course/en/unit0/introduction)
- [MCP introduction](https://modelcontextprotocol.io/docs/getting-started/intro)

The Hugging Face course and certification are explicitly free. MCP is not a replacement for task scheduling, state management, authorization, or integration checks.

## 11. Later tracks: AI applications, local models, and model training

These are deliberately deferred. Choose them based on an actual goal, not because every AI roadmap lists them.

### 11.1 Building AI-enabled applications

| Resource | Purpose | When to use it |
|---|---|---|
| [Agentic AI - Andrew Ng / DeepLearning.AI](https://www.deeplearning.ai/courses/agentic-ai/) | Python-first tool use, reflection, planning, evaluation, and workflow patterns. | When moving from using coding agents to implementing agent behavior. |
| [Claude with the Anthropic API](https://anthropic.skilljar.com/claude-with-the-anthropic-api) | APIs, context, structured output, tools, evaluation, retrieval, and caching. | Study selected gaps; avoid repeating an overlapping curriculum in full. |
| [Retrieval Augmented Generation - DeepLearning.AI](https://www.deeplearning.ai/courses/retrieval-augmented-generation-rag/) | Chunking, BM25, semantic/hybrid retrieval, ranking, grounding, and evaluation. | When an application needs private, changing, or domain-specific knowledge. |
| [Introduction to LangGraph - LangChain Academy](https://academy.langchain.com/courses/intro-to-langgraph) | State, human feedback, memory, checkpoints, and deployment. | When a Python workflow needs explicit orchestration. |
| [Microsoft Agent Framework](https://learn.microsoft.com/en-us/agent-framework/overview/) | Agent and workflow building integrated with the Microsoft/.NET ecosystem. | A strong first option for workplace transfer to your .NET background. |
| [.NET AI documentation](https://learn.microsoft.com/en-us/dotnet/ai/overview) | Model integration, tools, and AI application patterns in .NET. | To retain your existing backend architecture. |
| [AI SDK React/Next.js quickstart](https://ai-sdk.dev/docs/getting-started/nextjs-app-router) | Streaming UI, messages, and tool-aware interfaces. | As an implementation example, not a requirement to replace .NET with Node. |
| [Hugging Face Agents Course](https://huggingface.co/learn/agents-course/en/unit0/introduction) | Free agent fundamentals and hands-on framework examples. | An alternative learning route, not another mandatory duplicate foundation course. |

Choose one orchestration framework initially. Microsoft's current documentation describes Agent Framework as the successor to Semantic Kernel and AutoGen; check the status of the exact package or integration, because the reviewed C# quickstart includes a prerelease package.

The LangGraph introductory course is free and lists six hours of video. Check examples against current framework documentation.

DeepLearning.AI's Agentic AI page offers free video auditing with paid labs/assessments. The RAG page has mixed wording about audit versus preview access; verify enrollment terms before paying.

### 11.2 Running open-weight models locally

Start with the cloud-to-local course in section 5.4.

Additional official resources:

- [LM Studio documentation](https://lmstudio.ai/docs)
- [Ollama documentation](https://docs.ollama.com/)
- [llama.cpp project](https://github.com/ggml-org/llama.cpp)

Learn model licensing, quantization, RAM/VRAM requirements, context limits, inference latency, and compatibility with tool-calling workflows.

Compare local models on your own tasks before replacing a hosted setup. Smaller or local models may require tighter task decomposition and more constrained tool use.

Local inference does not by itself make a workflow offline or private: plugins, telemetry, external tools, gateways, and networked integrations may still transmit data.

### 11.3 Adapting and training models

| Sequence | Resource | Focus |
|---|---|---|
| 1 | [Practical Deep Learning for Coders - fast.ai](https://course.fast.ai/) | Training, validation, transfer learning, overfitting, and practical deep learning. The linked course is the 2022 edition; use current environment guidance. |
| 2 | [Hugging Face LLM Course](https://huggingface.co/learn/llm-course/chapter1/1) | Transformers, datasets, tokenizers, fine-tuning, evaluation, and advanced LLM topics. |
| 3 | [Finetuning Large Language Models - DeepLearning.AI](https://www.deeplearning.ai/short-courses/finetuning-large-language-models/) | An introductory fine-tuning workflow and data preparation. Supplement with current tooling and parameter-efficient methods. |
| 4, advanced | [Stanford CS336: Language Modeling from Scratch](https://cs336.stanford.edu/) | Tokenizers, transformers, training data, optimization, scaling, and alignment. |

Before fine-tuning, establish a prompting/retrieval baseline and a held-out evaluation set. Learn dataset quality, leakage prevention, supervised fine-tuning, and parameter-efficient methods such as LoRA.

CS336 expects calculus, linear algebra, probability, machine/deep-learning knowledge, PyTorch familiarity, and systems performance work. It also requires compute planning. It is not a prerequisite for agent-assisted development.

Use retrieval for access to changing or permissioned information. Consider fine-tuning for measured improvements in task behavior or performance. Fine-tuning is not a substitute for authorization or reliable access to current facts.

## 12. Demand evidence and independent validation

The original course shortlist was independently challenged by a research subagent on November 2, 2026.

### What the evidence supports

| Source | Observation | Limitation |
|---|---|---|
| [Stanford AI Index 2026, economy chapter, pp. 204-208](https://hai.stanford.edu/assets/files/ai_index_report_2026.pdf) | Lightcast's 2025 US postings included Python in 258,674 AI postings, agentic AI in 16,541, RAG in 12,609, and LangGraph in 4,294. | These are 2025 posting observations published in 2026, not full-year 2026 demand or a guarantee of employability. |
| [LinkedIn AI Labor Market Update, August 2026](https://delivery-p143253-e1476319.adobeaemcloud.com/adobe/assets/urn:aaid:aem:ef153078-1061-4817-82e7-a1c027d7a7d7/original/as/AI-Labor-Market-Update-August-2026-v2.pdf) | For April-June 2026, US AI Engineer hiring increased 64% year over year while overall US hiring declined 6.9%. | LinkedIn infers hiring through profile updates. This does not isolate coding-agent orchestration as a separate occupation. |
| [PwC Global AI Jobs Barometer 2026](https://www.pwc.com/gx/en/issues/artificial-intelligence/job-barometer/2026/2026-global-ai-jobs-barometer-full-report.pdf) | Skills in the most AI-exposed jobs change approximately twice as fast as in the least exposed. | AI exposure is an analytical classification, not proof of adoption or causation. |
| [Stack Overflow Developer Survey 2025](https://survey.stackoverflow.co/2025/ai) | 51% of professional developers used AI tools daily; 46% distrusted accuracy versus 33% who trusted it. | Adoption and sentiment data are not hiring data or proof of productivity gains. |

These sources support continued investment in AI-related engineering skills. They do not prove that employers require the specific course certificates listed here or that multi-agent setups always improve productivity.

Do not treat all reports as wholly independent datasets: some research may rely on overlapping advertisement sources.

### Independent review conclusions

- The overall direction toward AI-enabled engineering is relevant.
- Course popularity and course-specific hiring preference were not established.
- Too many overlapping coding-assistant introductions would waste a senior developer's time.
- Evaluation, authorization, deployment, and reliability were underweighted in the initial broad curriculum.
- One orchestration framework is enough initially.
- Model training should remain a separate specialization.

### Adjustment after your clarification

Your immediate goal is operating coding agents, not building a RAG application or training a model. Accordingly:

- Advanced harness use, specialist delegation, worktrees, handoffs, and independent verification move to the front.
- The cloud-to-local workflow course becomes strongly recommended.
- RAG, general agent frameworks, and deep learning move to later tracks.
- Building a small coordinator around existing coding tools becomes the natural next implementation step.

The practical recommendation is grounded in your goal and current tool capabilities, not a claim that one specific multi-agent architecture dominates the job market.

## 13. Completion checklist

### Ready to operate a dependable coding-agent workflow

- [ ] I can turn a feature into observable acceptance criteria.
- [ ] My repository has correct setup, build, and test instructions.
- [ ] I understand which context and permissions each worker receives.
- [ ] I can distinguish profiles, subagents, independent sessions, and agent teams.
- [ ] I use separate workspaces when concurrent writers need isolation.
- [ ] I account for ports, test databases, generated files, and other shared resources.
- [ ] I can review an exact candidate independently.
- [ ] I assess agent findings instead of accepting them automatically.
- [ ] I verify integrated behavior, not only isolated worker results.
- [ ] I preserve meaningful assertions and inspect generated tests.
- [ ] I can identify incomplete work and checks that were not run.
- [ ] I measure time to accepted change, intervention, defects, and cost.
- [ ] I keep sensitive actions behind appropriate approval boundaries.

### Ready to automate coordination

- [ ] A manual version of the workflow already succeeds repeatedly.
- [ ] Inputs, outputs, task state, and ownership are explicit.
- [ ] Failures, timeouts, cancellation, and retry exhaustion are visible.
- [ ] Repair loops and concurrency have limits.
- [ ] Candidate changes invalidate outdated review/check evidence.
- [ ] Secrets and permissions remain controlled in automated execution.
- [ ] A human escalation path exists.

## 14. Keeping the roadmap current

Tool capabilities, authentication rules, SDK APIs, pricing, and experimental status change quickly. Recheck official documentation before implementation.

Every month:

1. Read release notes for your primary harness.
2. Check changes to subagent context, permissions, and instruction loading.
3. Re-run a few representative tasks before adopting a new model or configuration.
4. Review costs and failed runs.
5. Remove obsolete instructions and unused tools.

Every quarter:

1. Reassess whether parallelism saves total engineering effort.
2. Review sandbox and credential boundaries.
3. Update your evaluation tasks and regression cases.
4. Choose the next skill based on an observed bottleneck rather than tool novelty.

**Definition of success:** You remain accountable for engineering decisions while agents reliably perform bounded implementation, investigation, testing, and review work with measurable benefit.
