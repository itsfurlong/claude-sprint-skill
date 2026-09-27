# sprint

A Claude skill for running any multi-part project as a collaborative sprint between Claude and a human: code, a research paper, a website, a business plan, anything with more than one step. "Sprint" means sit down and work until this part is finished.

It turns a request into a written guide of small, paste-ready steps the user reviews before anything changes, then executes one step at a time, stopping for a go-ahead after each. It exists to fix two failure modes: producing a lot of work before checking whether the approach is right, and burning tokens re-reading whole files or re-establishing context that a fresh, bounded prompt would not need.

## What it does

1. Finds the project's working doc, or asks, and creates `WORKING.md` only when the user confirms the project is new.
2. Triages the request into a table (type, priority, in or out this sprint) and states the ground rules once, up front.
3. Breaks the work into single-purpose, paste-ready steps, each ending in a done check, and stops for review before step 1.
4. Executes one step at a time, stopping after each. It never touches the live version and never deletes files; anything recommended for deletion goes to `_recommended-for-deletion/`.
5. Hands off every checkpoint (push, publish, send) for the human to ship, then logs what shipped in the working doc.

Full detail in [`skills/sprint/SKILL.md`](skills/sprint/SKILL.md).

## Project types

The working doc names the project type, which sets the done check, the checkpoint method, and what only the human does.

| Type | Done check | Checkpoint |
|---|---|---|
| Code | Compiles, lints, tests pass | Local commit (Claude never pushes) |
| Writing / research | Claims sourced, citations resolve | Versioned copy |
| Website | Pages load, links work, responsive | Commit or versioned copy |
| Business | Numbers recompute, assumptions marked | Versioned copy |
| Other | A check agreed in the working doc | Versioned copy |

## Requirements

The skill needs access to the project's files, so it runs in Claude Cowork or Claude Code. In a plain chat with no file access, it says so in one line and stops. Git is optional: code projects use it, everything else checkpoints with versioned copies. No personal setup or global instructions required.

## Using it

**In Cowork:** save it as a skill on your account and invoke it by name (`/sprint`) in a project with a connected folder.

**In Claude Code:** copy the `skills/sprint/` folder into `.claude/skills/sprint/` in any repo. Claude Code picks up skills from that path automatically.

## License

Use it, copy it, change it for your own projects.
