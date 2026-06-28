# Example & validation harness — Acme SaaS

A worked end-to-end example, and the way to check the skills actually work.

## Contents

- `inputs/` — raw design-system inputs you'd feed the generator:
  - `tokens.css` — color / spacing / type tokens
  - `components.md` — component library notes (with `Use instead of` cues)
  - `brand.md` — the design stance / principles
- `.design/knowledge.md` — the artifact the generator **should** produce from
  those inputs (at the same canonical path a real project uses). This is the
  reference for "what good (sharp, specific) looks like."
- `sample-diff.patch` — a new UI panel with **planted divergences** plus the
  expected `/turtleneck review` verdict at the bottom.

## How to validate the skill

**1. `init` — does it extract sharply?**
Run `/turtleneck init` pointed at `inputs/`. Compare its output to
`.design/knowledge.md`. It should be just as specific: real component names, real token
values, falsifiable principles. Apply the thinness test — if a line could've been
written without seeing Acme's inputs, the extraction was too shallow.

**2. `review` — grounded, correct, conservative?**
Run `/turtleneck review` with `.design/knowledge.md` as the artifact and
`sample-diff.patch` as the target. Check it against the expected verdict in the patch:
- Catches all 3 BLOCKERs and 2 NITs.
- Grades them right (hand-rolled button / competing primaries / raw `<a>` are
  BLOCKERs; off-scale spacing / bad weight are NITs).
- **Stays quiet** on the Field-wrapped input, on-scale gaps, and the nested plain
  `<section>`. Flagging those is a false positive — the failure mode the tool
  exists to avoid.

**3. `pass` — does it fix behind approval?**
Run `/turtleneck pass` on the same target. It should propose a diff (swap the
hand-rolled button for `<Button>`, make Cancel secondary, use `<TextLink>`, fix
the spacing/weight) and apply **only after you approve** — never silently.
