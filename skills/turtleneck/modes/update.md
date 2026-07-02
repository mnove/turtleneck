# Mode: update — refresh the artifact, merged (behind approval)

`/turtleneck update`. Re-distill the design-knowledge artifact after the design
system changed — **merging** into the existing `.design/knowledge.md` rather than
regenerating it from scratch. This is what you run when you added a component,
renamed a token, or revised the brand doc. It **modifies the artifact**, so it is
gated behind a human-approved diff, exactly like `pass`.

The difference from `init`: `init` writes a fresh artifact and would blow away
human edits. `update` **preserves what a human confirmed** and only touches what
actually moved.

**First, load [_shared.md](_shared.md)** to read the current `.design/knowledge.md`
(if it's missing, there's nothing to update → tell the user to run
`/turtleneck init` first). The output shape is [../schema.md](../schema.md), same
as `init` — read it.

## The merge policy (the heart of this mode)

Compare the current artifact against the current design system, entry by entry:

- **Preserve confirmed knowledge.** Any entry *without* a `⚠️ INFERRED — confirm`
  tag has been ratified by a human (they deleted the tag or hand-edited it). Do
  **not** silently overwrite it. If its source changed, surface the conflict in
  the diff for the human to resolve — don't just clobber it.
- **Refresh drifted entries.** When a source moved (a token value changed, a
  component gained/lost a variant), update the entry to match reality.
- **Add new discoveries.** New components/tokens/principles found in the system
  get added, tagged `⚠️ INFERRED — confirm` (like `init` does for anything
  inferred).
- **Flag removals, don't delete.** If an entry's source no longer exists (a
  component was deleted), mark it `⚠️ STALE — confirm removal` rather than
  dropping it. The human decides whether it's truly gone.

## Process

1. **Inventory the current system** the same way `init` does — glob components,
   tokens/theme files, brand docs, `.design/examples/`. Read deeply.
2. **Diff against the current artifact** using the merge policy above. Categorize
   every change as preserve / refresh / add / flag-stale.
3. **Propose the merged artifact as a diff.** Show what changes and, briefly, why
   (e.g. "`space-5` added — new value in tokens.css"; "`LegacyModal` marked STALE
   — no longer in the codebase"). Group by category so the human can scan it.
4. **Get explicit approval before writing.** Offer real choices: approve all /
   approve additions+refreshes but let me review stale flags / let me pick /
   cancel. No file is written until approved.
5. **Write the approved result** to `.design/knowledge.md`. Update the `Last
   updated:` line and the `Sources:` list if inputs changed.
6. **Run the thinness test** (from `init`) on any new/refreshed entries — new
   filler is still filler.

## After writing

Tell the user plainly:
- What changed, by category (added / refreshed / flagged stale / preserved).
- To **review new `⚠️ INFERRED — confirm` and `⚠️ STALE — confirm removal` tags**
  and resolve each (confirm, correct, or delete).
- That confirmed entries were left intact.

## Boundaries

- **Approval-gated.** The artifact is not modified before the user approves the
  proposed diff. This is the line separating `update` from a silent regeneration.
- **Preserve human intent.** Never overwrite a confirmed (untagged) entry without
  surfacing the conflict. The artifact is theirs.
- **Artifact only.** `update` edits `.design/knowledge.md`, never UI code. Fixing
  UI is `pass`; explaining a rule is `explain`.
- **Don't invent.** New entries must come from the system, not from generic design
  sense. An honest gap beats a fabricated rule.
