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
