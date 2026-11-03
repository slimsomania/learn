---
name: revalidate
description: Freshness sweep of ROADMAP.md. Re-checks every linked resource and time-sensitive claim (versions, prices, GA/preview status, course availability, demand numbers) with independent validators and applies the corrections. Use monthly, before starting a new phase, or when the learner asks "is this still current?". Takes an optional section number to limit scope.
---

# Revalidate the roadmap

1. **Pick the scope.** Use the section given in the arguments, or else the whole of `ROADMAP.md`. Check the date of the last pass in `validation-log.md`. Prioritize the sections the learner is currently working on (see `PROGRESS.md`) and the fast-moving ones: harness features (§3–5), model and tool names (§4, §7), prices and certificates (§11).
2. **Extract claims.** List every linked resource and every time-sensitive statement (versions, "experimental", "beta", prices, dates, percentages), each with its section number.
3. **Validate in parallel.** Send the claims to `resource-validator` instances in batches of about 8, grouped by section. Send only the claims, not the surrounding prose.
4. **Apply.** Make the minimal edits the validators list under "Required roadmap edits". For a `Replace`, the replacement must itself be verified; otherwise run `research-topic` for that item. Keep evergreen items and mark them as such.
5. **Log.** Append an entry to `validation-log.md` with counts (kept, replaced, dropped, unverified) and the list of edits.
6. **Report.** Summarize what changed and anything the learner has to decide.
