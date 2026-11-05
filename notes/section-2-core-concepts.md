# §2 Core concepts: notes

Source: [ROADMAP.md §2](../ROADMAP.md#2-core-concepts-learn-these-words-precisely). Readings done: Building effective agents (Anthropic), A practical guide to building AI agents (OpenAI), Effective context engineering for AI agents (Anthropic). Finished 2026-11-05.

Each term has my own definition, then a **Check** line where the roadmap or the readings add something to it or correct it.

## Terms in my own words

**Model.** The deep "brain" behind the tool. It makes the decisions, so the agent can act like a person would.
- Check: the model only produces text and tool calls. It never runs anything itself; the harness does that. Choosing a model decides quality, cost, latency and context size.

**Harness.** The infrastructure around the model: the tools it can use (bash, MCP, file edits), how it works through a response to get the job done, and how the model talks to those tools.
- Check: also permissions, sessions and context management. Put the same model in a different harness (Claude Code, Codex, Copilot) and it behaves differently.

**Role / custom agent / subagent.** A role is just an MD file. The orchestrator, or I directly, can ask for that agent to do a job. A subagent is a unit the parent agent can start up.
- Check: the file holds instructions, allowed tools and an optional model. A subagent starts with **fresh context** and returns only a summary, so the parent's context stays clean. My "tester" is a config, not a separate model.

**Agent team.** In Claude Code, a way to start a team of agents that work on a task in parallel while an orchestrator coordinates them.
- Check: teammates are full sessions with a **shared task list**, and they **message each other**. Subagents only report back to their parent. Agent teams are still experimental (`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`) and need an interactive session. They give more parallelism, but coordination costs more. Build on subagents + hooks first (§3.2).

**Skill.** Teach the agent how to do one specific thing, wrap it up as a skill, and use it when it's needed, so the agent doesn't invent the procedure from scratch each time.
- Check: a skill is `SKILL.md` plus optional scripts. Only its description sits in context until it's needed, and then the whole thing loads. Write it once and every role can reuse it.

**Hook.** An action that runs before or after the agent does something. Before: a guardrail that checks whether the agent may access something. After: a formatter that runs once code has been generated.
- Check: hooks are **code, so they enforce rules; prompts only suggest**. Exit code 2 blocks the action on blocking events such as `PreToolUse`, and stderr goes back to the agent. `PostToolUse` can't block, and exit code 1 doesn't block either. There are more events than pre/post tool, for example `SessionStart`, `Stop` and `TaskCompleted` (§3.4).

**MCP.** Something like a tool, but not one specific tool; closer to an API. A more complex tool the agent uses to talk to external systems.
- Check: MCP is a **protocol**, not a tool. An MCP *server* exposes tools, resources and prompts, and any harness that speaks MCP can use it. Build it once and use it in Claude Code, Codex and Copilot. Deep dive: §6.3, Phase E.

**A2A.** A protocol. I don't know enough about it yet and need to go deeper.
- Check: a standard protocol for agents to call **other agents**, possibly from different vendors or services. An agent publishes an Agent Card (what it can do), and others send it tasks. v1.0 since Mar 2026 (§6.3). Rule of thumb: MCP connects an agent to tools; A2A connects agents to agents. Only matters once agents live in separate services, so it waits for Phase E.

**Worktree.** A standalone copy of the repository where an agent works without conflicting with an agent in another tree. Like a branch in the GitHub repo.
- Check: it's a **local git** feature, not GitHub. A worktree is an extra working directory that shares the same `.git` and has its own branch checked out (`git worktree add`). It isolates **files only, not ports, databases or credentials**, so two agents running the app at once can still collide. Practice: Phase B.

**Eval.** Not clear to me yet; needs more study.
- Check: a repeatable test of model or agent behavior, i.e. a test suite for prompts and agents. It's the only way to know whether a change helped. Start small in Phase A with the §10 metrics; the full course is in §6.4 (Phase E).

## Prompt structure

From *Effective context engineering for AI agents* (Anthropic). Split an agent's system prompt into distinct, labelled sections (XML tags or Markdown headers):

1. **Background information**: who the agent is and what context it works in.
2. **Instructions**: what to do.
3. **Tool guidance**: which tools to use, when, and how.
4. **Output description**: what to return and how the result will be read.

The same article says to aim for the "right altitude": specific enough to guide behavior, but no brittle if-then rules. Start with a minimal prompt and add to it only when a test shows something failing.

Use this whenever I write a role file (`.claude/agents/*.md`, `.github/agents/*.agent.md`) or a system prompt.

## Patterns mapped to my current workflow

*Drafted by Claude from the readings; edit it if you see it differently.*

- My current loop is describe → plan mode → implement → Copilot PR review.
  - Plan → implement is **prompt chaining**.
  - Copilot review is a single, manual pass of **evaluator-optimizer**: I loop on its findings by hand.
- In Phase B: one planner splitting work across FE/BE implementers is **orchestrator-workers**. Reviewer and tester running side by side on the same commit is **parallelization**. Sending a task to a cheap or strong model by type is **routing** (Phase C).

## What a reviewer should and shouldn't see

*Drafted by Claude from the readings; edit it if you see it differently.*

- Sees: the spec or acceptance criteria, the exact diff/commit, the checks that ran and their results.
- Doesn't see: the implementer's narrative or reasoning, which biases the review (§3.3), or the full chat history.
- The general rule from context engineering: give each worker the smallest set of high-signal tokens that does the job.
