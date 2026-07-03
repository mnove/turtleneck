# turtleneck — design-critic skill

turtleneck gives a coding agent a **grounded, advisory design-critic** ability.
It is augmentation, not enforcement: it advises and improves on request; it never
blocks builds or gates CI.

One command, five modes: `/turtleneck init | update | review | explain | pass`.

This note is **conditional**, not always-on. Apply it only when the user asks to
review or improve UI — do not critique design unprompted.

## When the user asks to review or improve UI

Triggers: "turtleneck", "review this design", "does this match our design
system", "design review / pass", "explain this finding", "set up design
knowledge", or `/turtleneck …`.

1. **Find the artifact.** Look for `.design/knowledge.md`. If it's missing, offer
   to run `/turtleneck init` first. Do **not** critique from generic design sense
   — ungrounded advice is the failure mode this tool exists to avoid.
2. **Ground every finding** in that artifact. Cite the specific component, token,
   principle, or example. Never invent rules.
3. **Be conservative.** A false BLOCKER costs more trust than a missed NIT. Fewer,
   sharper findings. If the work is consistent, say so plainly.
4. **Respect the read-only / mutate split.** `review` and `explain` report only
   and never edit. `pass` (UI) and `update` (the artifact) may edit, but only
   behind a human-approved diff. Ambiguous or empty input must never land in a
   mutating mode — show the menu. Never silently rewrite.

## The one command

| Mode | What it does |
|------|--------------|
| `/turtleneck init` | Distill the project's design system into `.design/knowledge.md` (run once per project). |
| `/turtleneck update` | Refresh the artifact after the design system changes — merged, behind approval. |
| `/turtleneck review` | Report grounded findings against the artifact. Read-only. |
| `/turtleneck explain` | Explain why a finding/component/token/principle stands. Read-only. |
| `/turtleneck pass` | Review + apply UI improvements behind an approved diff. |
| `/turtleneck` (no arg) | Show the discovery menu. |

The skill lives in [skills/turtleneck/](skills/turtleneck/). The knowledge
artifact is per-project, committed to the user's repo, and owned by them — not a
black box.
