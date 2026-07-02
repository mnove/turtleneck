# Mode: review — report findings (read-only)

`/turtleneck review`. Review UI work against the design-knowledge artifact and
report where it diverges. **Read-only — you report findings and never modify any
file.** (Applying fixes is `pass`, behind approval.)

**First, load [_shared.md](_shared.md).** It defines the artifact-loading step,
the grounding + conservatism rules, the severity levels, and the finding /
`SUMMARY` format. This mode adds only the read-only specifics below.

## Do

1. Follow `_shared.md`: load `.design/knowledge.md` (if missing → tell the user to
   run `/turtleneck init` first; do not critique from generic design sense),
   identify the target, and review it against the artifact's lenses —
   **Components** (hand-rolled / wrong choice), **Tokens** (off-scale, raw hex,
   bad weight/size), **Principles** (broken, with a concrete pointer),
   **Anti-patterns** (forbidden things).
2. Apply the grounding rule: only flag what ties to a specific artifact line. If
   the work is consistent, say so plainly.
3. Output findings in the `_shared.md` format, ending with the `SUMMARY:` line.
4. **If there was at least one finding**, add exactly one line after `SUMMARY:`,
   pointing to `explain` for the reasoning:

   ```
   Run `/turtleneck explain <finding>` for the reasoning behind any of these.
   ```

   One line, once — not per finding. Skip it entirely on a clean report (0
   blockers, 0 nits); there's nothing to explain. Keep it to this single pointer:
   the value of `review` is terse, grounded findings, not upsell.

## Example output

```
BLOCKER  src/components/SignupForm.tsx:42
  Hand-rolled a <button className="..."> for the submit action. The artifact's
  Button component is the required choice for actions ("Use instead of: a raw
  <button>").
  Fix: replace with <Button variant="primary">Sign up</Button> from
  @/components/ui/button.

NIT  src/components/SignupForm.tsx:58
  margin of 20px is off the spacing scale (allowed: 4/8/16/24px via space-*).
  Fix: use space-4 (16px) or space-6 (24px).

SUMMARY: 1 blocker, 1 nit.
Run `/turtleneck explain <finding>` for the reasoning behind any of these.
```

## Boundaries

- **Read-only.** Never edit, never write, never apply a diff in this mode. If the
  user wants fixes applied, point them to `/turtleneck pass`.
- **Grounded only.** No free-floating opinions, no rules not in the artifact, no
  critiquing untouched code.
- **Not enforcement.** Advisory. You don't block builds or gate CI.
