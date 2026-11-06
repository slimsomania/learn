# Progress

Tracks the phases in [ROADMAP.md](ROADMAP.md). For each topic: study -> explain the knowledge check -> follow the selected SDK example -> apply it to a real task. Tick items only after doing them; editing this roadmap does not complete the learning. Log §10 task metrics so single-agent and team setups can be compared on evidence.

## Current phase

**Phase 0: Safety and access** (ROADMAP §1.1; existing VPS audit in §5.1; access/budget questions below). Started: _not yet_. Security precedes tool grants; pending optional CLI/cloud/VPS work does not prevent approved IDE study in A.

Recommended core order: **0 -> A -> B -> E -> D**; D can run alongside E after B is repeatable. C and F are optional. No fixed weekly calendar or mandatory extra harness/certificate.

## Phase status

| Phase | Machine | Status | Started | Exit criterion met |
|---|---|---|---|---|
| 0. Safety and access | both + existing VPS | not started | | |
| A. Single-agent mastery | both | not started | | |
| B. Coding-agent team | both | not started | | |
| E. Building AI applications | both; approved APIs | not started | | |
| D. Scripted orchestration | home; work if approved | not started | | |
| C. Local inference (optional) | home | not started | | |
| F. Model adaptation / internals (optional) | home + cloud GPU | not started | | |

### Phase E study and application

Use the assigned sources in ROADMAP §6. Do not mark a live API/cloud criterion complete from a stub-only exercise.

| Slice | Study / knowledge check | Application evidence | Status |
|---|---|---|---|
| E1 (§6.1) | Agent/workflow choice, tool contracts, success/risk boundaries | Bounded user workflow, baseline and initial labeled examples | not started |
| E2 (§6.2) | Native API, schemas, refusal/tool continuation, bounded state | One validated API/tool loop with failure and limit checks | not started |
| E3 (§6.3) | Error analysis, held-out data, judge calibration, traces | Failing CI regression, retained report and visible request/tool trace | not started |
| E4 (§6.4) | General MCP before C# SDK; framework state and approval | Two read-only tools, compatible host, real denial and restart recovery | not started |
| E5 (§6.5) | Retrieval/reranking, authorization, freshness/deletion | Comparison and document/permission checks if RAG is included; literacy required | not started |
| E6 (§6.6) | Reliability, Python packaging, containers, identity and release operations | Fault checks, small Python component, hosted release/rollback and runbook | not started |

## Courses and resources completed

| Date | Item | Hours | Notes / practice evidence | Notes |
|---|---|---|---|---|
| 2026-11-05 | §2 Read first: Building effective agents (Anthropic) | | [notes/section-2-core-concepts.md](notes/section-2-core-concepts.md) | Five patterns; skip the tooling section |
| 2026-11-05 | §2 Read first: A practical guide to building AI agents (OpenAI, web) | | same | Single / manager / decentralized; guardrails |
| 2026-11-05 | §2 Read first: Effective context engineering for AI agents (Anthropic) | | same | Source of the prompt-structure checklist |
| | **§2 total** | ~2–3 h (roadmap estimate) | Exit criterion met | Follow-ups: A2A (optional §6.7) and evals (§6.3/§10) flagged for deeper study; completed readings need not be repeated |

## Task metrics (ROADMAP §10)

| Date | Task | Setup (single / team / models) | Time to accepted change | Human minutes | Defects found later | Useful vs false review findings | Cost |
|---|---|---|---|---|---|---|---|

## Open questions / decisions

- Work, check yourself first (ROADMAP §3.6), then ask the admin about what's missing:
  - [ ] Copilot CLI installs and signs in
  - [ ] "Copilot" is assignable on issues (coding agent)
  - [ ] Optional only: Codex shows up as an approved Agent HQ partner agent
  - [ ] Confirm allowed models and whether any non-OpenAI models are enabled; vendor diversity is not required
  - [ ] Confirm policy coverage for delegated/built-in/fallback, PR review, permission-approval and background model paths; main-model selection alone does not establish no-Anthropic compliance
  - [ ] Confirm approved application-model endpoints, authentication, quota/budget and telemetry/data handling; Copilot access does not establish Azure API entitlement
  - [ ] Confirm Azure/Actions/repository-protection access and workplace Docker licensing before the corresponding practice
- VPS (Hermes, ROADMAP §5.1):
  - [x] Note the exact Hetzner plan: CX23, 2 vCPU / 4 GB / 40 GB (2026-11-02)
  - [ ] Inspect/back up the source build and data, preserve local patches, and check applicable release/security fixes. Last reported build: v0.22.0+119 (2026-09-24), not a live check
  - [ ] Update through the actual install's supported method and verify the running gateway, not only checkout version; no frozen minimum version is a security guarantee
  - [ ] Set container limits that fit 4 GB/40 GB, then check host RAM headroom with `free -m` and `docker stats` during a light job
  - [ ] Check §5.1 authorization, approvals, backend/mount/network isolation, credential passthrough, hooks/plugins and secret protection
  - [ ] Which Claude plan do you have: Pro, or Max/Ultra?
- Home, when selecting API/extra-reviewer practice:
  - [ ] Check available API access and credit/budget privately; do not put keys in notes/chat (application API billing is separate from consumer subscriptions)
  - [ ] If choosing codex-plugin-cc, check existing ChatGPT/Codex authentication and limits or API access; a paid ChatGPT plan is not inherently required
  - [ ] Choose a small inference/evaluation budget before E2/E3; check separate cloud/Search/telemetry costs before E5/E6
  - [ ] Optional F only: assess §8.1 readiness and confirm actual cloud GPU availability/VRAM/cost before training; no fixed free quota assumed

## Revision note

2026-11-05: synchronized phase order and study/application checkpoints with the validated roadmap revision. Existing completion records and the checked VPS specification were preserved. No new learner progress, account approval, live deployment or successful lab is claimed.
