---
name: wrap-continue
description: Mid-task context reset — light capture, smart push, hot-resume pickup prompt rendered inline. Use when recycling a long-lived thread to free context while continuing the same work. NOT for end-of-day wrap — use /wrap for that.
license: MIT
metadata:
  version: 0.5.0
  category: workflow
  domain: session-management
  status: stable
  platforms: All
keywords:
  - wrap
  - continue
  - context
  - reset
  - checkpoint
  - token
  - compact
  - resume
---

# Wrap + Continue

Mid-task context reset. Light capture. Use when the session is being recycled to free context — the work is NOT done, you're resuming immediately in a fresh thread.

Triggered by: `/wrap-continue`, "wrap continue", "wrap and continue", "checkpoint", "context reset", "trim and continue".

If the session is actually done for the day, use `/wrap` instead — that runs full retrospective capture.

## Steps

### 1. Commit & Push (mandatory commit, smart push)

Check `git status`. Everything uncommitted is at risk when context clears. Commit all meaningful changes now — this is not optional.

If nothing is uncommitted, note it and move on.

**Push behavior — default ON with two smart skips, no prompt:**

- **Skip silently** if there is nothing to push (working tree already matches remote HEAD).
- **Skip with a one-line note** if the tracked remote is named `upstream`, or the branch is `main`, `master`, or `develop` on a non-`origin` remote. Status table note: `Push skipped — upstream/protected remote, push manually if intended`.
- **Otherwise push to origin.** No confirmation needed.

_Why these skips exist: commit protects against thread crash; push protects against disk failure and machine switches. Skipping upstream/protected remotes avoids accidental CI triggers and the upstream-PR landmine (prior Ghostty incident)._

### 2. Light Capture (optional — only if non-obvious)

Save feedback, corrections, or decisions that surfaced this session and aren't yet in memory. Keep this short. The bar is: "would I lose this if context cleared right now?"

- New feedback → save to `.claude/projects/<path>/memory/feedback_*.md`
- New reference → save to `reference_*.md`
- Major architectural decision, project state change, or in-flight work update → update `ORCHESTRATOR.md` Decision Log and In-Flight Work sections. This applies to ANY thread in a project that has an `ORCHESTRATOR.md` — not just orchestrator threads. Implementation threads change state too (CVs generated, roles applied, conventions locked) and that state must be written back before context clears.

Skip if nothing non-obvious came up. Don't run full retrospective — that's `/wrap`.

### 3. Hot-Resume Pickup Prompt

Generate a compact pickup prompt the user can paste at the start of the next thread to restore context exactly where work left off.

The pickup prompt must include:

- **What we're building** — one sentence on the project + task
- **Where we are** — current status, what just shipped (with commit SHA if applicable)
- **What's next** — the immediate next action (specific, not vague)
- **Key context** — anything non-obvious that the next thread won't know from the codebase (design decisions, constraints, open questions, gotchas)
- **Working directory** — the repo path so the next thread can orient immediately

**3a. Render the pickup prompt inline** as a fenced code block between the cut lines shown in the Output Format below. This is the primary deliverable — the user must be able to see it, scroll to it, and re-copy from it. Do not skip this step.

**3b. Then also copy to clipboard** via `pbcopy`:

```bash
cat <<'EOF' | pbcopy
[prompt content here]
EOF
```

The inline render is the source of truth. The clipboard is a convenience copy of the same content.

If `pbcopy` is unavailable (non-macOS), skip silently — add a note in the status table that clipboard copy was unavailable.

## Output Format

```
  ╭─────────────────────────────────────────────────────────╮
  │  ↻  WRAP + CONTINUE                                     │
  ╰─────────────────────────────────────────────────────────╯

  ┌──────────┬──────────────────────────────────────────────┐
  │ Git      │ 2 commits — pushed to origin (abc1234)       │
  │ Capture  │ 1 feedback saved / skipped                   │
  │ Clipboard│ Copied to clipboard                          │
  │ Repo     │ Clean, up to date with origin                │
  └──────────┴──────────────────────────────────────────────┘
```

Then the wrapped-present, cut-lines, and the pickup prompt rendered inline:

```
              ╱╲ ╱╲
             ╱  ╳  ╲
        ╭───────┴───────╮
        │       │       │
        │ ──────┼────── │
        │       │       │
        │  DO NOT OPEN  │
        │  UNTIL NEXT   │
        │   SESSION     │
        ╰───────────────╯

   ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
```

```
## Pickup prompt (paste at start of next thread)

[project + task context]
[current status + last commit SHA]
[immediate next action]
[non-obvious context / constraints]
Working directory: ~/Code/[project]
```

```
   ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ✂
```

**Pickup prompt is on your clipboard.** Paste it at the start of the next thread.

## Judgment Calls

- **Nothing uncommitted?** Still generate the pickup prompt — that's the whole point.
- **Nothing to push?** Skip push silently. Still commit if anything was uncommitted.
- **Upstream remote detected?** Skip push with the one-line note. Don't ask.
- **Big mid-session correction?** Save it as feedback before clearing — future threads will thank you.
- **Project has ORCHESTRATOR.md?** Update it — even if this is an implementation thread, not the orchestrator. Any state that changed this session (decisions, conventions, role status, files created) goes in Decision Log or In-Flight Work before context clears.
- **User seems in a hurry?** Commit + push (if applicable) + pickup prompt only. Skip capture.
- Keep the whole flow under 5 exchanges. This is speed work, not ceremony.

## Future Setup Path (deferred)

If a second user adopts this skill, add a config file at `~/.claude/skills/wrap-continue/config.json` with: (a) origin remote name override, (b) custom protected branch patterns, (c) opt-out of auto-push. Not built now — premature for a one-user skill.
