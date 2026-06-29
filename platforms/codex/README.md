# turtleneck on Codex

turtleneck runs natively on Codex as a skill. Codex skills use the same shape as
Claude Code's — a folder with a `SKILL.md` (`name` / `description` frontmatter,
`$1`/`$ARGUMENTS` placeholders, slash invocation). This folder
(`platforms/codex/turtleneck/`) is **generated** from the shared source in `src/`
by `npm run build`, so it's a complete, self-contained set of real files — nothing
to resolve, nothing to symlink.

Only the `SKILL.md` wrapper differs from the Claude Code build:
- frontmatter is the [agentskills.io](https://agentskills.io/specification) shape
  (`allowed-tools` as a space-separated string; `argument-hint` under `metadata`);
- the router reads the mode from `$1` instead of a named `arguments` field.

The `modes/` and `schema.md` here are byte-identical to the Claude build — both
come from `src/body/`. **Don't edit these files;** edit `src/` and rebuild.

## Install

**Easiest — ask your agent.** Tell Codex:

> Clone https://github.com/mnove/turtleneck and install the turtleneck skill
> into my Codex skills directory.

**Manual.** Codex discovers skills under `~/.agents/skills/` (user scope) or
`<repo>/.agents/skills/` (repo scope). Copy the skill folder there:

```sh
git clone https://github.com/mnove/turtleneck.git
mkdir -p ~/.agents/skills
cp -r turtleneck/platforms/codex/turtleneck ~/.agents/skills/turtleneck
```

> The folder is already self-contained (real files), so a plain `cp -r` is all
> you need. A repo-scoped install works the same into `<your-repo>/.agents/skills/`.

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
- **Re-sync after pulling updates:** the installed folder is a copy, not a live
  link — re-copy to pick up changes:
  `rm -rf ~/.agents/skills/turtleneck && cp -r platforms/codex/turtleneck ~/.agents/skills/turtleneck`.
- **Uninstall:** `rm -rf ~/.agents/skills/turtleneck`.

## For maintainers

This folder is **generated** — see [the build](../../README.md#build). Edit the
shared source in `src/body/` (router + modes + schema) or the per-platform config
in `src/platforms.json`, then run `npm run build` to regenerate every platform.
Never hand-edit files here; the build's `--check` mode will flag drift in CI.
