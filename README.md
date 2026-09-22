# sprint

A Claude skill. Turns a bug report, feature request, or batch of small fixes for a codebase-backed project into a bounded, step-by-step implementation guide with paste-ready prompts, then executes it one step at a time, stopping for a go-ahead after each step.

It exists to fix two failure modes: writing a lot of code before checking whether the approach is right, and burning tokens re-reading whole files or re-establishing context that a fresh, bounded prompt would not need.

## What it does

1. Confirms the project has a working doc, or creates one first. Nothing else in the skill functions without one.
2. Triages whatever's being asked into a table: type, priority, in-or-out this sprint.
3. States the project's ground rules once, up front, instead of repeating them in every step.
4. Breaks the work into phases and single-purpose, paste-ready steps, each ending in a compile/test check.
5. Executes one step at a time and stops for a go-ahead after each one.
6. Hands off every commit with a copy-paste terminal block for status, log, and push. Automatic, never on request.
7. Updates the working doc after the sprint closes: what shipped, what's pushed, what's verified, what's left open.

Full detail in [`skills/sprint/SKILL.md`](skills/sprint/SKILL.md).

## Using it

**As a Cowork or claude.ai skill:** save it to your account and invoke it by name (`/sprint`) in any chat.

**As a Claude Code skill:** copy the `skills/sprint/` folder into `.claude/skills/sprint/` in any repo. Claude Code picks up skills from that path automatically.

## License

Use it, copy it, change it for your own projects.
