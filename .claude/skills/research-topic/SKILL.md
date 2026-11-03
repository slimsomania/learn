---
name: research-topic
description: Add a new topic to ROADMAP.md, or refresh an existing one, through an independent research → validate → integrate loop. Use when the learner asks "what should I learn for X", "add X to the roadmap", or "is X still worth it".
---

# Research a topic

1. **Scope.** Restate the topic, which roadmap phase it belongs to (A–F), and which machine it applies to (work or home). Check whether `ROADMAP.md` already covers it.
2. **Research.** Launch `resource-researcher` with the topic and phase. For a broad topic, split it into 2–3 subtopics and launch the researchers in parallel.
3. **Validate independently.** Turn the researcher output into a plain claims list (item, link, claimed cost/time/status), with no rationale, and send it to `resource-validator`. Large lists can go to parallel validators in batches of about 8.
4. **Resolve.** Drop every item marked `Drop` or `Unverified`. For each `Replace → X`, make sure X itself was verified; if it wasn't, send it through validation as well.
5. **Integrate.** Edit the relevant `ROADMAP.md` section in its existing style (see the CLAUDE.md conventions). Add new sources to its Sources list, and update §11 if there's new demand evidence.
6. **Log.** Append an entry to `validation-log.md` using the template in that file.
7. **Report.** Give the learner a summary of what was added, replaced and dropped, and why, in a few lines.
