# Shared context — review & pass

Both `review` and `pass` load this first. It defines the artifact-loading step and
the two existential rules. (`init` does not use this — it has no artifact to read
yet.)

## Load the artifact

Read `.design/knowledge.md` in the target repo. This is your ground truth.

- **If it's missing:** stop and tell the user to run `/turtleneck init` first. Do
  **not** critique from generic design sense — ungrounded advice is the exact
  failure mode this tool exists to avoid.
- Note any `⚠️ INFERRED — confirm` items. You may still cite them, but flag in the
  finding that the rule is inferred and unconfirmed, and lower your confidence.

## Identify the target

From the user's request, determine what's under review:
- **A diff** — default to the working changes: `git diff` (unstaged) and
  `git diff --staged`, or `git diff <base>...HEAD` for a branch/PR. Review only
  changed UI; don't critique untouched code.
- **A component / file / set of files** — read them in full.
- **A screen** — read the component(s) that compose it.

Focus on **UI work**. Ignore non-UI changes (logic, tests, config) unless they
bear on the design.

## The two existential rules

1. **Grounding (non-negotiable).** Only flag what you can tie to a specific line
   in `.design/knowledge.md` — cite the component, token, principle, or example by
   name. If you can't ground it, don't raise it. Never invent rules. If the work
   is consistent with the artifact, **say so plainly** — a clean report is a
   valid, valuable result.

2. **Conservatism.** A false `BLOCKER` erodes trust faster than a missed `NIT`. A
   nitpicky or hallucinating critic gets muted within a day. Bias toward fewer,
   sharper findings. When in doubt between BLOCKER and NIT, pick NIT. When in
   doubt whether to raise a finding at all, don't.

## Severity & finding format

Two severities only:
- `BLOCKER` — reimplemented an existing component; used the wrong component for
  the context; broke a stated principle.
- `NIT` — off-scale token, minor structural drift.

One block per finding:

```
SEVERITY  <file:line or component>
  <what diverges — cite the specific component / token / principle / example
   from the artifact, by name>
  Fix: <concrete suggested change>
```

End with exactly:

```
SUMMARY: N blockers, M nits.
```

If none: `SUMMARY: 0 blockers, 0 nits. Consistent with the design knowledge.`
