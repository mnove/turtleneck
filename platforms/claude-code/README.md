# turtleneck on Claude Code

turtleneck is one Claude Code skill (`skills/turtleneck/`) — a router `SKILL.md`
plus mode files under `modes/`. On Claude Code it's invokable as a single command
with three modes. Its bodies are written agent-agnostic so the same files port to
other agents later.

## Install

**Easiest — ask your agent.** Tell Claude Code:

> Clone https://github.com/mnove/turtleneck and install the turtleneck skill
> into my Claude Code skills directory.

It will clone the repo and symlink `skills/turtleneck` into `~/.claude/skills/`
(approve the clone/symlink when prompted).

Or do it manually — pick one:

**A. Clone + symlink (recommended — get updates by pulling this repo)**
```sh
git clone https://github.com/mnove/turtleneck.git
ln -sfn "$(pwd)/turtleneck/skills/turtleneck" ~/.claude/skills/turtleneck
```
(If you're already inside a clone, just the `ln -sfn "$(pwd)/skills/turtleneck"
~/.claude/skills/turtleneck` line.)

**B. Copy**
```sh
cp -r skills/turtleneck ~/.claude/skills/turtleneck
```

**C. Project-scoped** — put it under `.claude/skills/turtleneck` in the target
repo if you want it available only there.

Then restart Claude Code so it registers the skill. It's available as:

| Command | Mode |
|---------|------|
| `/turtleneck init` | Distill your design system → `.design/knowledge.md` |
| `/turtleneck update` | Refresh the artifact after changes (merged, behind approval) |
| `/turtleneck review` | Report grounded findings (read-only) |
| `/turtleneck explain` | Explain why a finding/component/token/principle stands (read-only) |
| `/turtleneck pass` | Review + apply UI fixes, behind an approved diff |
| `/turtleneck` | Discovery menu |

Typing `/turtleneck ` shows the `[init | update | review | explain | pass]` argument hint.

## Usage

1. **Once per project:** `/turtleneck init`, pointing it at your design system. It
   writes `.design/knowledge.md`. Review the `⚠️ INFERRED — confirm` tags, then
   commit the artifact.
2. **Per change:** `/turtleneck review` (report-only) or `/turtleneck pass` (apply
   behind approval). With no target specified, the critic defaults to the working
   diff (`git diff` / `git diff --staged`).

## Updating

There's no auto-update — refresh the folder when a new version lands.

- **Symlinked (install A):** pull + rebuild; the symlink already points at the
  rebuilt folder.
  ```sh
  cd turtleneck && git pull && npm run build
  ```
  Then restart Claude Code.
- **Copied (install B):** pull + rebuild, then re-copy over the old install.
  ```sh
  cd turtleneck && git pull && npm run build
  rm -rf ~/.claude/skills/turtleneck
  cp -r skills/turtleneck ~/.claude/skills/turtleneck
  ```
- **No clone?** Ask your agent: *"Update turtleneck — pull the latest and reinstall
  the skill."*

## Notes

- The knowledge artifact lives in the **target project's** repo at
  `.design/knowledge.md` — not in this repo and not in your global config. It's
  yours: committed, diffable, owned by you.
- Drop blessed reference screenshots into the target repo's `.design/examples/`;
  `init` references them and the critic uses them as the bar.
- **Uninstall:** `rm ~/.claude/skills/turtleneck` (removes only the symlink/copy,
  not your repo).
