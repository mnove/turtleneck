# Mode: init — generate the design-knowledge artifact

`/turtleneck init`. Distill this project's design system into the
design-knowledge artifact that `review` and `pass` consume. Run once per project,
before the others. This is the make-or-break step: a critic fed thin knowledge
produces generic, ignorable advice. The whole value is being **sharper than the
base model's generic design sense.** Spend the effort here.

The exact output format is [../schema.md](../schema.md). Read it before you start.
This file is the *process*; `schema.md` is the *shape*. (`init` does not use
`_shared.md` — there's no artifact to read yet.)

## Inputs you accept (any subset)

Ask for what's missing only if it would materially change the artifact — don't
block on having everything.

- **A component library / design system** (e.g. shadcn + custom components) —
  read the components themselves: names, variants, intended use.
- **Design tokens / theme** — colors, spacing, type scale (CSS vars, a theme
  file, `tailwind.config`, a tokens JSON).
- **A brand or style document** — prose guidelines, voice, design stance.
- **Screenshots / exported designs** — Figma exports, images of "good" UI.
- **Blessed example screens** — existing screens the user calls canonical.

## Process

1. **Inventory the inputs.** Glob for a components directory; a theme/tokens file
   (`tailwind.config.*`, `theme.*`, `tokens.*`, `globals.css`); brand docs;
   any `.design/examples/` the user pre-populated. Ask the user to confirm or
   point you at anything you can't find.
2. **Read deeply, not broadly.** Open real component source and read variant
   definitions, not just file names. Extract actual token values. Specificity
   comes from reading the code, not summarizing it.
3. **Distill into the schema.** Fill the [../schema.md](../schema.md) template:
   - **Components** — for each: what it's *for* and, the highest-value field,
     **`Use instead of`** (what people hand-roll or mis-pick that the reviewer
     should flag). Order by how often mis-used.
   - **Tokens / scales** — the *allowed values* and the *canonical reference*.
     Concrete values, not "the spacing scale."
   - **Principles** — the project's real stance, 2–6 sharp, falsifiable
     statements. No brand input → write `Principles: none provided`. Never
     invent a stance.
   - **Reference examples** — 2–3 pointers to canonical "good" screens.
4. **Tag every inference** `⚠️ INFERRED — confirm`. Strong draft, not ground truth.
5. **Write to `.design/knowledge.md`.** Create `.design/` if needed, plus
   `.design/examples/.gitkeep` as an obvious place for blessed screenshots.
6. **Run the thinness test (below) before declaring done.**

## The thinness test — the bar to clear

Read your own artifact and ask of each line:

> Could the base model have written this without ever seeing this project?

If yes, it's generic filler — cut it or sharpen it with a real name, value, or
file path. Reject: "Use the spacing scale" (→ name the values + reference),
"Use consistent components" (→ name the component + its `Use instead of`),
"improve contrast / use whitespace" (→ delete; design-blog advice). If more than
a couple lines fail, read the inputs harder before shipping.

## After writing

Tell the user plainly:
- Where it lives (`.design/knowledge.md`) and that it's **theirs** — committed,
  diffable, inspectable. Not a black box.
- To **review the `⚠️ INFERRED — confirm` tags** and delete each tag once
  confirmed (or correct it).
- They can drop blessed reference screenshots into `.design/examples/`.
- `review` and `pass` now have something to ground against.

## Boundaries

- You only **read and distill** here. No UI critique, no UI edits — those are
  `review` and `pass`.
- Don't invent rules the inputs don't support. An honest "none provided" beats a
  fabricated principle the critics will later cite as fact.
