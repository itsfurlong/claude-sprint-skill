# sprint

A Claude skill for running any multi-part project as a collaborative sprint between Claude and a human: code, a research paper, a website, a business plan, anything with more than one step. "Sprint" means sit down and work until this part is finished.

It turns a request into a written guide of small, paste-ready steps the user reviews before anything changes, then executes one step at a time, stopping for a go-ahead after each.

## Why it exists

1. **Answers "what does doing this even require?"** Before any work starts, the skill lays out what the project actually takes. It doesn't just do the work.
2. **Maximizes prompt velocity.** Velocity is speed in a direction. Speed in the wrong direction costs more time than it saves, so the skill confirms direction before it moves fast.
3. **Makes projects human-driven and AI-powered, not the other way around.** The user drives the process instead of getting driven by it.
4. **Reduces errors with built-in human checks.** Every step ends in a pause. Sometimes that's just "continue?", and that's fine. The pause is a set place for either the user or Claude to flag an error, a complication, or an opportunity before the next step builds on it.
5. **Maximizes token efficiency.** Fresh, bounded prompts instead of re-reading whole files or rebuilding context the next step doesn't need.

**Who it's for:** people who want to build something and use AI as a tool. Not for those who just want AI to make things for them.

## What it does

1. Finds the project's working doc, or asks, and creates `WORKING.md` only when the user confirms the project is new. The working doc stays private by default: inside a repo, it's added to `.gitignore`.
2. Triages the request into a table (type, priority, in or out this sprint) and states the ground rules once, up front.
3. Lays out what the work requires (inputs, decisions, access, unknowns), then breaks it into single-purpose, paste-ready steps, each ending in a done check, and stops for review before step 1.
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
