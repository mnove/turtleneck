# turtleneck on Codex

turtleneck runs natively on Codex as a skill. Codex skills use the same shape as
Claude Code's — a folder with a `SKILL.md` (`name` / `description` frontmatter,
`$1`/`$ARGUMENTS` placeholders, slash invocation). The skill body, modes, and
schema are **shared with the Claude Code build** — `modes/` and `schema.md` here
are symlinks into `skills/turtleneck/`, so there's a single source of truth.

Only the `SKILL.md` wrapper differs from Claude Code:
- frontmatter is the [agentskills.io](https://agentskills.io/specification) shape
  (`allowed-tools` as a space-separated string; `argument-hint` under `metadata`);
- the router reads the mode from `$1` instead of a named `arguments` field.

## Install

**Easiest — ask your agent.** Tell Codex:

> Clone https://github.com/mnove/turtleneck and install the turtleneck skill
> into my Codex skills directory.

**Manual.** Codex discovers skills under `~/.agents/skills/` (user scope) or
`<repo>/.agents/skills/` (repo scope). Copy the self-contained skill folder there
— use `cp -rL` so the shared symlinks are resolved into real files:

```sh
git clone https://github.com/mnove/turtleneck.git
mkdir -p ~/.agents/skills
cp -rL turtleneck/platforms/codex/turtleneck ~/.agents/skills/turtleneck
```

> Use `cp -rL` (capital L), not a plain symlink — it follows the internal
> `modes/` and `schema.md` symlinks so the installed folder is self-contained.
> A repo-scoped install works the same into `<your-repo>/.agents/skills/`.

Then start Codex. Type `/` to see `turtleneck` in the slash menu.

## Usage

Same three modes as everywhere:

| Command | Mode |
|---------|------|
| `/turtleneck init` | Distill your design system → `.design/knowledge.md` |
| `/turtleneck review` | Report grounded findings (read-only) |
| `/turtleneck pass` | Review + apply, behind an approved diff |
| `/turtleneck` | Discovery menu |

### Steps

1. **Once per project:** `/turtleneck init`, pointing it at your design system. It
   writes `.design/knowledge.md`. Review the `⚠️ INFERRED — confirm` tags, then
   commit the artifact.
2. **Per change:** `/turtleneck review` (report-only) or `/turtleneck pass` (apply
   behind approval). With no target specified, the critic defaults to the working
   diff (`git diff` / `git diff --staged`).

## Notes

- The knowledge artifact lives in the **target project's** repo at
  `.design/knowledge.md` — not in this repo and not in your global config. It's
  yours: committed, diffable, owned by you.
- Drop blessed reference screenshots into the target repo's `.design/examples/`;
  `init` references them and the critic uses them as the bar.
- **Re-sync after editing the skill:** the installed folder is a resolved copy
  (`cp -rL`), not a live link — re-run the copy to pick up changes:
  `rm -rf ~/.agents/skills/turtleneck && cp -rL platforms/codex/turtleneck ~/.agents/skills/turtleneck`.
- **Uninstall:** `rm -rf ~/.agents/skills/turtleneck`.

## Keeping it in sync

`modes/` and `schema.md` are symlinks into `skills/turtleneck/`, so any edit to
the shared body is reflected here automatically — there's nothing to regenerate.
Only `SKILL.md` is Codex-specific. (If a third platform is added later, that's the
point to introduce a small build step to generate each wrapper from one source.)
