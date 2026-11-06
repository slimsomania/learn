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

## 2026-11-02: Structure and phase order of ROADMAP.md (§0–§13), no resource claims re-checked
Validators: 1 structural read-through (internal consistency only; no web checks, no links changed) · Verdict: phase order A→F is sound; 11 structural issues found
Findings:
- Sequencing: Hermes upgrade + security checklist (§5.1) is filed under Phase D, but Hermes already runs below the v0.22.2 CVE minimum, so it belongs before Phase A. Admin questions (§3.6) and the OpenAI access decision (PROGRESS) have lead time and gate Phase B, but no phase owns them.
- Phase A has no baseline metrics, yet Phase B and §13 compare "single agent vs team" (§10 says baseline first).
- §3.1 says start with implementer + reviewer only; the Phase B exercise uses planner + 2 implementers + tester + reviewer at once. §3.4 level 2 (harness subagents) is not assigned to any phase.
- Phase C exercise (b) repeats Phase B's cross-model review. §13 "one local model used in a real role" contradicts §7.3 (offline batch jobs only on this laptop). §7 (Phase C material) sits after §6 (Phase E) with no phase label.
- §6.6 and §12 say security moved to Phase B, but Phase B's goal, exit criterion and §3.5 materials don't include it.
- §6 order puts optional LangGraph (§6.3) before MCP, evals and security, which the capstone needs; §6.1 stacks two first-principles agent-loop courses (pick-don't-stack).
- §8 puts ~60–80 h of theory (Karpathy, HF course) before the first fine-tune, so the Phase F exit criterion is ~2 months away at 8 h/week.
- §1 table has no machine column (CLAUDE.md convention; PROGRESS has one). Phase D has no work-side (Copilot-only) content although PROGRESS lists "then work".
- §13 groups A–D in one list, so it can't serve as per-phase exit checks.
Edits (approved by the learner, same day): §1 new Phase 0 + Machine column, A baseline metrics, B split into weeks 3–4 / 5–6 with security in the exit, D 9–12 wk, E months 4–6, F month 7+ · §3.4 levels mapped to phases · §3.5 rows tagged machine + phase, security row added · §3.6 admin questions = Phase 0; work and home exercises split into two steps · §4 and §7 phase intros; Phase C exercise reworked (one model / routing / + local batch) · §5 week split + work-variant placeholder; §5.1 checklist = Phase 0 · §6 reordered: MCP 6.3, evals 6.4, security 6.5, RAG 6.6, LangGraph 6.7 (optional); Claude API course optional · §8 practice first (post-training → Unsloth/TRL → Karpathy → HF → nanochat → CS336) · §12 row updated · §13 per-phase checklist · PROGRESS.md Phase 0 row and current phase
Open: work-side Phase D needs a research-topic pass before it can be written

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

## 2026-11-05: Study-first curriculum, resource alignment and approved implementation (§0-§13)

Scope: apply the learner-approved revision after the read-only review and independent research/validation in this conversation. "Current" means valid for 2026 approaches, tools and practices, not recently published. This is a scoped public-content and curriculum pass, not an exhaustive fresh run of every legacy link or any enrolled lab.

Validators: 4 independent topic/content passes plus 1 independent training-readiness pass in the final alignment round; earlier researcher/validator rounds checked the added API, retrieval, operations and North American role sources. Final document checks cover phase routing, material/application coverage and consistency. Verdict counts below concern the eight explicitly disputed course/credential entries, not URL totals: Keep: 1 (MCP introduction) · Keep selected: 3 (MCP Advanced Topics, HF MCP, red-teaming methods) · Downgrade/retain optional: 3 (Maven evals, DataCamp GH-300, Claude API specialization) · Unverified/watch: 1 (learner's Claude certification eligibility) · Drop: 0 whole courses. Hosting links replaced: 3 Skilljar course URLs -> verified Academy URLs.

Edits:
- §1: core order 0 -> A -> B -> E -> D; C/F optional. Removed fixed calendar gates, mandatory extra agents/harnesses and the local-inference prerequisite. Added validity-not-recency rule, cost/access prerequisites, one continuing pet project and study -> knowledge check -> example -> project contract.
- §1.1, §3: security before tool grants; targeted work/home instruction/role/CI material. Qualified harness-specific fields, context inheritance, model fallbacks, utility/permission-review paths and hook limitations. Work stays on cleared Copilot/non-Anthropic/Azure surfaces; pending optional access does not block approved IDE learning.
- §4-§5: consolidated Codex plugin setup, preserved optional cross-model tools and removed guaranteed independent-error claims. Specified matched comparisons and the Copilot-only headless route with IDE fallback. State/identity, bounded retries/budgets, cancellation and human approval have named study sources. Hermes trigger is optional; VPS safety, source/data preservation, actual running-version checks and resource limits replace static security-floor/version assertions.
- §6: explicit E1-E6 concepts/API/evals/framework+MCP/retrieval/reliable-delivery lessons, knowledge checks and applied outputs. Added structured outputs/failures, held-out evaluation, telemetry, authorization, ingestion/deletion/provenance, resilience, bounded Python packaging/testing, containers, Azure identity/health/release/rollback and retained production telemetry.
- §6.4: general MCP education remains required before C# SDK practice. Kept HF MCP as an alternative and Advanced Topics as selected legacy/advanced depth. Quoted its pre-2026-07-28 notice; deprecated capabilities are not described as removed. Current SDK guide is versioned; reviewed local examples precede unversioned package execution.
- §6.6-§6.7: reading OWASP does not replace practical testing; red-teaming lab compatibility remains unverified. Agentic 2026 PDF is watch because extraction failed. Python framework/A2A depth remains conditional; no universal hiring/adoption claim from documentation or downloads.
- §7-§8: optional local lab uses measured memory/context/latency, not guaranteed model class/speed or GPU shopping. Added minimal micrograd/PyTorch/HF training-readiness bridge before LoRA; PEFT concepts precede Unsloth implementation, with pre-training data splits and post-export evaluation. No guaranteed free GPU quota, fixed training time or per-experiment price. Karpathy/HF/nanochat/CS336 retained as qualified depth.
- §9-§10: reviewer example is shell-free, test execution isolated, base/candidate evidence and integration checks explicit. Starter task counts are not statistical proof; comparisons account for task difficulty, resolved configurations and answer leakage.
- §11-§13: four directly accessible US/Canada role examples, required/preferred distinctions and nonrepresentative-sample limits. Removed unsupported fine-tuning percentage and strict cross-source skill ranking. Historical data dates remain explicit. Optional exam prep retained; partner-only Claude credential stays watch. Checklist matches the core/optional sequence without arbitrary skill/pattern/Telegram/Agent-HQ gates.
- PROGRESS.md: synchronized sequence and E checkpoints, updated follow-up references/access questions, preserved the three completed §2 readings and existing checked VPS specification. No newly completed learner work is inferred.

Content qualifications: selected DLAI public syllabi support the assigned topics but authenticated lessons/notebooks were not executed. Evergreen pages were reread for supersession notes; older valid concepts stay. Current references qualify stale Copilot module fields/hooks, unsupported model parameters/auto-approval demonstrations, old CI runtimes and Azure Login v1 examples. New-app OTel guidance differs from the older distro; Aspire is a development viewer and ingestion needs its own protection. SDK/client interoperability, checkpoint recovery and export behavior still require practice-time checks.

Open: actual work policies and API/cloud budgets; local/VPS runtime state and model acceleration/headroom; course enrollment/lab entitlement and red-teaming Important Note; unread Agentic 2026 PDF; MAF Magentic package-status conflict; certification eligibility; consumer-bot auth terms; real deployment/training/export costs and compatibility. Historical sources explicitly retaining Nov 2 checks were not represented as newly revalidated PDFs.

Verification: focused PowerShell assertions after each edit slice; final source-coverage, local-link, section-reference, completed-progress preservation and whitespace checks recorded in the implementation outcome. No build/lint/test suite exists in this documentation workspace. Archives and study notes were not edited; no commit was made.

Final integration review: 1 additional independent source-validator pass over 8 qualification groups plus 1 read-only curriculum consistency review. Repaired the single consistency finding (conditional export must not become a mandatory Phase F gate). Applied source corrections: HF's shutdown/exit diagram is not MCP protocol, stdio is not automatic sandboxing, and the versioned C# guide now has an explicit matching package pin. No further full-course deletion was introduced.

Check implementation note: the initial progress-preservation matcher used a locale-dependent read and failed; rerunning with explicit UTF-8 confirmed all three original completed rows and the existing checked VPS fact were unchanged. This was a checker encoding defect, not altered learner progress.
