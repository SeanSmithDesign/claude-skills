# Changelog — wrap skill

## v1.5.1 (2026-04-25)

### Added

- **Wrapped-present ASCII visual** — continue-mode output now renders a self-labeling wrapped-present art block ("DO NOT OPEN UNTIL NEXT SESSION") between the status table and the pickup prompt. Pairs with the wrap-it-up box's gift metaphor. Replaces the plain "Hot Resume" label; no external banner needed.
- **Opening and closing cut-lines** — dashed cut-line before the pickup prompt fenced block; dashed scissor line (`✂`) after. Frames the hot-resume prompt as a discrete take-away unit.
- **Clipboard auto-copy via `pbcopy`** — after rendering the pickup prompt, the model pipes it to `pbcopy` so you can paste directly into the next thread without manual selection. Gracefully skipped (with table note) when `pbcopy` is unavailable (non-macOS).
- **`Clipboard` status row in continue-mode table** — reports `✓ Pickup copied (pbcopy)` on success or `— pbcopy unavailable, copy manually` when skipped.

### Changed

- Section heading renamed: "Pickup Prompt — Continue Mode (Hot Resume)" → "Pickup Prompt — Continue Mode". The wrapped-present visual is now the label; the parenthetical subtitle is no longer needed.
- Output Format section prose updated to reference the wrapped-present and cut-lines for continue mode.
- Version bumped from 1.5.0 → 1.5.1.

## v1.5.0 (2026-04-25)

### Added

- **Volume-threshold guard** — `/wrap continue` now checks `git status --porcelain | wc -l` before staging. If dirty path count exceeds 15, execution halts and prompts the user with a breakdown by top-level directory plus three resolution options: commit everything, commit only inferred session paths, or abort for manual review. Motivated by a real incident where a continue wrap silently staged 207 unrelated dirty paths.
- **`/compact` vs. `/wrap continue` documentation** — new section in SKILL.md explaining the distinction: `/compact` trims history in the same thread; `/wrap continue` produces a hot-resume prompt for a clean-slate fresh thread. Helps users pick the right tool.

### Changed

- Version bumped from 1.4.0 → 1.5.0.
- Added `status: beta` to frontmatter (was implied, now explicit).
