# turtleneck

A reusable **design-critic capability for coding agents.** Point it at any UI
work — a diff, a component, a screen, a PR — and it reviews that work against
*your project's* design knowledge, and, on request, applies improvements.

It's an **augmentation, not an enforcement system.** It advises and improves; it
does not block builds or gate CI. Design judgment — hierarchy, spacing, component
choice, brand fit — is fuzzy, semantic work where an LLM is differentiated and a
deterministic linter can't help. turtleneck builds on that strength.

## The idea in one picture

The single most important decision: **per-project knowledge is generated; the
critic is fixed and universal.**

```
/turtleneck init  →  .design/knowledge.md   (per project, committed to your repo)
                            ↑ consumed by ↓
        /turtleneck review   (reports grounded findings — read-only)
        /turtleneck pass     (applies improvements — behind an approved diff)
```

`init` distills *your* design system into one Markdown artifact. `review` and
`pass` are written once and reused everywhere; they just consume that artifact.
Only the artifact is per-project — and it lives in **your** repo, diffable and
inspectable, not in a black box.

## One command, three modes

turtleneck is a single slash command with three modes (like `/turtleneck init`).
`review` and `pass` share grounding/conservatism rules via a shared context file;
the modes themselves are kept distinct so the read-only / mutate boundary is
explicit.

| Mode | Role | Mutates? |
|------|------|----------|
| `/turtleneck init` | Distill your design system → `.design/knowledge.md`. Run once per project. | Writes the artifact only |
| `/turtleneck review` | Report where UI work diverges from the artifact, with grounded findings. | No — read-only |
| `/turtleneck pass` | Review **plus** apply improvements, behind a human-approved diff. | Yes — after approval |
| `/turtleneck` | Show the discovery menu. | No |

**Mutate safety:** `pass` is the only mode that changes files, and the router
will only enter it on an explicit, unambiguous `pass`. Empty/unknown/ambiguous
input shows the menu — it never lands in `pass` by accident, and even then no
file is touched before you approve the proposed diff.

## Install

**Easiest — ask your coding agent:**

> Clone https://github.com/mnove/turtleneck and install the turtleneck skill
> into my Claude Code skills directory.

Your agent will clone the repo and symlink `skills/turtleneck` into
`~/.claude/skills/`. Approve the clone/symlink when prompted, then restart Claude
Code. Type `/turtleneck` to confirm it's there.

**Manual (deterministic):**
```sh
git clone https://github.com/mnove/turtleneck.git
ln -sfn "$(pwd)/turtleneck/skills/turtleneck" ~/.claude/skills/turtleneck
```

More options (copy, project-scoped) in
[platforms/claude-code/](platforms/claude-code/).

## Quick start (Claude Code)

1. **Install the skill** (see above), then restart Claude Code.
2. **Generate your design knowledge** — point it at your design system:
   ```
   /turtleneck init
   ```
   Feed it any subset of: a component library, design tokens/theme, a brand or
   style doc, screenshots, blessed example screens. It writes
   `.design/knowledge.md`. **Review the `⚠️ INFERRED — confirm` tags**, then
   commit it.
3. **Review or improve UI** against it:
   ```
   /turtleneck review        # report-only
   /turtleneck pass          # review + apply, behind approval
   ```

## What it is / isn't

**Is:** one agent skill that gives any agent a grounded design-critic ability,
specific to your project.

**Isn't:** a linter, a CI gate, an import-restriction enforcer, or an npm
package. No deterministic enforcement layer. No build is blocked. Nothing is
published to a registry.

### Non-goals (this version)
- No deterministic linting / ESLint config / import restrictions.
- No CI gating or build-blocking.
- No npm package or registry publishing.
- No automatic, unapproved UI mutation.
- It does **not** guarantee adherence — it augments. Reliable run-to-run
  enforcement would need a deterministic layer this version deliberately omits.

## Why it works (and its limits)

Two things are existential and are baked into the skill (shared by `review` and
`pass` via `modes/_shared.md`):

- **Grounding** — findings tie back to *your* artifact, by name. The whole value
  is being sharper than the base model's generic design sense. A critic fed thin
  knowledge produces ignorable design-blog advice — so `init` works hard to be
  specific, and the critic refuses to raise anything it can't ground.
- **Conservatism** — a false `BLOCKER` erodes trust faster than a missed `NIT`.
  A nitpicky or hallucinating critic gets muted within a day. The critic biases
  toward fewer, sharper findings, and says so plainly when work is consistent.

Honest limits: the generated knowledge is a strong draft, not ground truth
(review the inferred tags). It's probabilistic — it misses things and varies run
to run. That's acceptable for advice, and not a substitute for enforcement.

## Repo layout

```
skills/turtleneck/               # the skill — agent-agnostic source of truth
  SKILL.md                       #   thin router: argument-hint + dispatch + mutate-safety rule
  modes/
    _shared.md                   #   grounding + conservatism + artifact loading (review & pass)
    init.md                      #   distill the design system → the artifact
    review.md                    #   report grounded findings (read-only)
    pass.md                      #   review + apply, behind approval
  schema.md                      #   the artifact format (used by init)
platforms/
  claude-code/                   # install notes for Claude Code (the supported platform today)
examples/
  acme-saas/                     # a worked example: inputs → .design/knowledge.md → a diff to review
AGENTS.md                        # short, conditional routing + guardrail note
notes/idea.md                    # the original project brief
```

`platforms/` is structured so additional agents (Cursor, Codex, Gemini, …) can be
added later without touching the skill. Only Claude Code is populated today.
