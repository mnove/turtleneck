# Mode: pass — review AND apply (behind approval)

`/turtleneck pass`. Review the UI, then **apply** improvements to bring it in
line with the artifact — but only after a human approves the diff. This is the
**only mode that modifies files.** A pass mutates UI and can introduce
regressions or churn, so the approval gate is not optional.

The flow is always: **review → propose edits → human approves → apply.** Never
collapse it. Never silently rewrite. (The router already guarantees you only
reach `pass` on an explicit, unambiguous request — never by accident.)

**First, load [_shared.md](_shared.md)** — same artifact-loading, grounding,
conservatism, and finding format as `review`. This mode adds the apply step.

## Step 1 — Review

Follow `_shared.md` exactly as `review` does: load `.design/knowledge.md` (missing
→ tell the user to run `/turtleneck init` first; do not pass against generic
design sense), identify the target, produce grounded conservative findings, and
present them with the `SUMMARY:` line.

If the review finds **0 blockers, 0 nits**, report that the work is already
consistent and **stop** — there's nothing to apply. Don't manufacture churn.

## Step 2 — Propose edits as a diff

For the findings you'll act on, draft the edits and **show them as a proposed diff
before applying.**

- **Fix blockers first.** Nits are optional — confirm whether to include them, or
  default to blockers-only to keep the change tight.
- **Minimal, surgical edits.** Change only what a finding requires. Don't
  reformat, rename, or "improve" untouched code. Churn erodes trust as fast as a
  bad finding.
- Every edit must trace to a finding, which traces to the artifact. If you can't
  ground an edit, don't make it.

Get **explicit approval** before writing anything. Use `AskUserQuestion` (or a
clear prompt) with real choices: approve all / blockers only / let me pick /
cancel.

## Step 3 — Apply only what's approved

After approval, apply exactly the approved edits — nothing more. Then:
- Summarize what changed and what was intentionally left (e.g. "applied 2
  blockers; skipped 1 nit per your choice").
- Leave the working tree reviewable; do **not** commit unless asked. The user
  reviews the real diff with their own tools.

## Boundaries

- **Approval-gated.** No file is modified before the user approves the proposed
  diff. This is the line separating `pass` from a silent rewrite.
- **Grounded only.** Every edit ties to a finding that ties to the artifact.
- **Conservative.** Fewer, sharper edits. Blockers before nits. Minimal diff.
  When in doubt, propose less.
- **Not enforcement.** Advisory. You improve on request, behind approval — you
  don't block builds or gate CI.
