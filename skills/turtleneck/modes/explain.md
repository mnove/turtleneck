# Mode: explain — trace the reasoning (read-only)

`/turtleneck explain`. Given a finding, a component, a token, or a principle,
explain **why** — trace the rule back to the artifact and back to *its* source
(the brand doc line, the token file, the component definition). This is the
teaching mode: it turns a verdict into an understanding. **Read-only — you
explain, you never modify any file.**

**First, load [_shared.md](_shared.md).** It defines the artifact-loading step and
the grounding rule, which apply here exactly as they do to `review`.

## What you can be asked to explain

Support both entry points:

1. **A finding from a prior `review`** — the natural pairing. "Why is that a
   blocker?" Take the finding and justify it: which artifact rule it violates, why
   that rule exists, and what the fix restores.
2. **A named thing, standalone** — a component ("why do we have `Button`?"), a
   token ("why is 20px not allowed?"), a principle ("what does 'restraint' mean
   here?"), or "explain this file" (walk its UI choices against the artifact).

## Do

1. Follow `_shared.md`: load `.design/knowledge.md`. If missing → tell the user to
   run `/turtleneck init` first; do not explain from generic design sense.
2. Locate the relevant artifact entry (component / token / principle / anti-pattern).
3. **Build the reasoning chain**, most specific first:
   - **The rule** — quote the artifact entry by name (e.g. the `Button`
     component's `Use instead of`, or the `space-*` scale).
   - **Its source** — where that rule came from: the brand doc statement, the
     token file value, the component source. Point at it concretely.
   - **Why it matters here** — the concrete consequence in this project (what
     breaks, what inconsistency it causes), not a generic design homily.
4. If the thing has a `⚠️ INFERRED — confirm` tag, say so: the rationale is a
   reasonable inference, not confirmed ground truth. Lower your confidence
   accordingly.

## The grounding rule still applies

Only explain what ties to the artifact. If the user asks you to justify something
the artifact doesn't cover, **say so plainly** — "That isn't in the design
knowledge, so I won't invent a rationale for it" — and optionally suggest they add
it via `/turtleneck update`. Never manufacture a principle to sound authoritative.

## Example output

```
Why "use <Button> instead of a raw <button>" is a BLOCKER:

  Rule — the artifact's Button entry: "Use instead of: a raw <button>, or a
  <div onClick>. Both are BLOCKERs." (.design/knowledge.md → Components → Button)

  Source — Button is your only component that wires variant, size, focus ring,
  and the destructive treatment. A raw <button> gets none of it. (from
  @/components/ui/button and the "one primary action per view" principle).

  Why it matters here — a hand-rolled button drifts from the indigo primary
  treatment and skips the a11y focus ring, so it looks almost-right and behaves
  worse. Swapping to <Button variant="primary"> restores both for free.
```

## Boundaries

- **Read-only.** Never edit UI, never edit the artifact. To change a rule, that's
  `/turtleneck update`; to apply a fix, that's `/turtleneck pass`.
- **Grounded only.** No rationale for rules that aren't in the artifact.
- **Teach, don't lecture.** Tie every "why" to *this* project. Generic
  design-blog reasoning is the thing this mode exists to replace.
