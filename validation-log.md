# Validation log

Append-only. One entry per validation pass (see the `revalidate` and `research-topic` skills).

Template:

```text
## YYYY-MM-DD: <scope>
Validators: <n> independent passes · Kept: n · Replaced: n · Dropped: n · Unverified: n
Edits: <section → change>
Open: <anything the learner must decide>
```

---

## 2026-11-02: Full revalidation sweep of ROADMAP.md (§2–§12), triggered by the learner spotting a supersession banner on "Building effective agents"
Validators: 13 independent passes (12 by section + 1 follow-up), ~110 claims · Kept: ~80 (≈60 of them with wording, version or time fixes; 9 marked evergreen) · Replaced: 13 (mostly moved URLs: Codex docs ×3 → learn.chatgpt.com, OTel GenAI semconv → own repo, Unsloth → unsloth.ai/docs, Aspire → aspire.dev, MS Learn deep links ×3, Claude Academy root ×2, MAF Learn path, Claude cert; plus the Agent SDK billing claim, the Copilot model list and the MAF "prerelease" claim) · Downgraded to optional: 11 · Dropped: 4 claims (Digital Applied MCP stat, "LangGraph intro predates v1", Ollama "≥0.14.3 for streaming tool calls", `/fleet` "no messaging") · Unverified, kept as watch or caveat: 8
Process gap found: "Building effective agents" (Dec 2024) was pre-approved as evergreen in CLAUDE.md, so nobody re-read it and the author's "much of the tooling landscape … has changed" banner was missed. Fixes: CLAUDE.md criterion 5 (evergreen is never pre-approved; re-read for supersession notes) and resource-validator step 1 (quote banners, report redirects, mark unreadable PDFs Unverified). Three PDFs that the validators couldn't parse (OpenAI guide, Stanford AI Index pp. 200–212, LinkedIn Aug 2026) were read with `pdftotext`; their figures were confirmed.
Edits:
- §2: Building effective agents relabeled "evergreen concepts only" with Anthropic's banner quoted; OpenAI guide dated 2025 (dated model names).
- §3.2: subagents can be messaged/nested (old "children don't talk" was wrong); agent-teams Windows note; Spec Kit commands and "widely adopted" wording; Agents SDK handoffs optional; multi-agent research ~4×/~15×.
- §3.4: hooks exit-2 semantics; Codex non-interactive URL.
- §3.5: Claude Academy course links and ~3 h (was 5–8 h); MS Learn module roots, cloud-agent naming; AGENTS.md read natively by Claude Code ≥2.1.277; per-tool skills folders; Hermes removed from AGENTS.md readers (unverified).
- §3.6: custom-agent frontmatter; `/fleet` date and credits; Agent HQ billing now GitHub AI Credits, still preview, two admin switches; full non-Anthropic model list; code-review sources and effort levels; per-model toggle scope unverified.
- §4: codex-plugin-cc install, review gate off by default, Codex pricing tiers; `agy` facts (no I/O tie-in); MEAI GA 10.x; **LiteLLM 2026 supply-chain compromises**; DLAI course time.
- §5: `-p` Manual mode and `--permission-prompts none`; `codex exec` flags (`--full-auto` gone); OpenAI three runtimes; Managed Agents added as optional; **billing: subscription still covers `-p`/SDK (Jun 15 split paused)**; Hermes ships claude-code/codex skills.
- §6.1–6.2: Agentic AI ~10 h; Claude API course Skilljar only; MAF GA since Apr 2026, AutoGen archived, Magentic experimental in .NET, package status.
- §6.3–6.4: LangGraph downloads, Radar Adopt→Trial, Essentials link; **MCP spec 2026-07-28** (Roots/Sampling/Logging deprecated), C# SDK 2.3; MCP courses re-hosted and downgraded; A2A under AAIF.
- §6.5–6.7: DLAI times; Hamel & Shankar; Maven $4,400; OTel semconv moved, still Development; Langfuse owned by ClickHouse; Aspire standalone added as the work-side viewer; OWASP IDs and editions; Red Teaming and RAG courses optional/scoped.
- §7: **Ollama skips the iGPU unless `OLLAMA_IGPU_ENABLE=1`** (old text had it backwards); LM Studio iGPU off by default; versions; model sizes; hardware prices reworded.
- §8: HF course scope; Unsloth URL and bf16 LoRA for Qwen3.5; DLAI time; nanochat ~$48/~2 h; CS336 Spring 2026; **AI Pro includes 200 Colab compute units/month**; Kaggle quota community-reported; RunPod exact prices.
- §11: InterviewStack and DEV merged as one source; Stanford and LinkedIn figures confirmed from the PDFs; LangChain survey URL; Digital Applied dropped; JetBrains 90% reworded; Q1 survey named; AI-103 no longer beta; GH-300 first; Claude cert optional/home.
- §12: Maven price.
Open (watch): Copilot per-plan model availability and whether the model toggle covers code review/CLI; whether Copilot code review can use Anthropic models (ask admin); Claude cert eligibility; DGX Spark Oct 2026 price; GenAI visualizer in the standalone Aspire image; HF MCP certificate issuance; Hermes reading AGENTS.md; newly posted small tool-calling models.

## 2026-11-02: Revalidation of Hermes Agent and the VPS trigger/auth claims (§5.1, §3.5)
Validators: 2 independent passes, 21 claims · Kept: 15 (9 with wording fixes) · Corrected: 3 (claude-code-action doesn't open a PR by default; `docker_forward_env` is `terminal.docker_forward_env`; approvals modes are smart/manual/off) · Resolved from watch: 1 (Hermes reads AGENTS.md, now officially documented) · Unverified: 3 (minimum VPS size, since Hermes publishes none; whether a personal bot running `claude -p` is allowed, not named in Anthropic's terms; CPU-VPS tok/s figures, secondary/undated only)
Edits:
- §5.1 intro: `advanced-delegation` plugin naming, context-file priority, cron delivery, release cadence.
- New VPS-size note: CX23 4 GB is the cheapest, unavailable on Nov 2; avoid 1 GB.
- Route A: scheduled tasks (formerly routines) beta header, limits (30/h per task + 100/h per account), untrusted fire payload, web-only tokens; claude-code-action pushes a branch + PR link, write access, PAT vs `GITHUB_TOKEN`, when `allowed_bots` applies; codex-action v1.13 with OpenAI or Azure OpenAI key.
- Route B: backend and image keys; exfiltration warning.
- Auth: setup-token scope and plans; terms wording quoted (explicit ban on routing plan credentials; personal-bot case moved to watch); `ANTHROPIC_AUTH_TOKEN` precedence; `--bare` default flip; Codex auth link moved to learn.chatgpt.com.
- Security checklist: **2026 Hermes CVEs (webhook auth bypass, MCP catalog supply chain, RCE); stay on latest, ≥v0.22.2**; deny-by-default allowlist; YOLO/`off` forbidden.
- Exercise: wording matches the action's real behavior.
- §3.5: Hermes back in the list of AGENTS.md readers.
Open: exact Hetzner plan (measure RAM headroom); which Claude plan (Pro vs Max+); watch Anthropic's terms on personal bots and the `-p` `--bare` default change.

## 2026-11-02: Hermes on a Hetzner VPS and laptop-off triggers
Validators: 1 · Kept: 11 · Dropped: 1 (Remote Control for this scenario, needs the machine on) · Downgraded: 1 (Ollama on VPS) · Unverified: codex-action version, OWASP ASI titles (PDF not read; secondary summaries), whether "own bot invokes `claude -p`" is explicitly allowed (interpretation)
Edits: new §5.1 (routes A/B, VPS auth, security checklist, VPS exercise) · §7.3 VPS inference line
Open: VPS specs (minimal Hetzner tier); is the Claude plan Pro or Max/Ultra (Max+ includes monthly API credits since 2026-10-07)

## 2026-11-02: Harnesses vs work/home constraints (Copilot Business/Enterprise without Anthropic; Gemini Pro; Codex; Hermes; Claude Code features)
Validators: 1 · Kept: 7 (Copilot custom agents/CLI/coding agent, per-agent models, Agent HQ, codex-plugin-cc, Hermes, Claude Code features) · Corrected: 2 (Copilot code review cannot merge with custom agents; demand framing) · Replaced: 1 (Gemini CLI → Antigravity CLI `agy` for AI Pro users) · Unverified: `excludeAgent` frontmatter, JetBrains survey primary page (403)
Edits: §3.2 agent-teams flag and limits · §3.5 AGENTS.md as the shared format · new §3.6 work-side Copilot-only team + admin checklist · §4 Agent HQ tiers, Gemini via `agy`, codex-plugin-cc setup · §5 `--bare`, Hermes positioning · §7 Phase C reviewer options · §11 multi-agent framed as a differentiator · §13 checklist split by machine
Open: ChatGPT plan / OpenAI API key? Work admin must enable CLI, cloud agent, Codex partner agent and the pinned models

## 2026-11-02: §7–8 local models and fine-tuning vs the learner's laptop (i7-1165G7, 16 GB, Iris Xe)
Validators: 1 · Kept: 3 (Ollama Anthropic API, cloud fine-tuning, nanochat cost) · Corrected: 3 (VRAM rule, tool list, realistic use) · Dropped: 1 (IPEX-LLM, archived Jan 2026) · Unverified: Foundry Local Intel catalog, AutoTrain status, full HF Jobs rates, T14 Gen 2 RAM slot layout
Edits: §7.1 rule for 16 GB + speed estimates · §7.2 Ollama Vulkan note, OpenVINO added, laptop model picks (qwen3.5:4b/9b, gemma4:e4b), no hardware purchase · §7.3 local model not suitable as a live Claude Code subagent; Phase C exercise reworked · §8 Colab/Kaggle/RunPod path, nanochat ~$73–100
Open: optional RAM upgrade to 32–40 GB (check PSREF)

## 2026-11-02: Initial roadmap (pre-repo)
Two original roadmaps (archive/2026-11) were each checked by an independent research subagent and then merged into ROADMAP.md. Results are recorded in archive/2026-11 ("Independent validation" and §12 "Demand evidence and independent validation").
