# Sprint Skill — Working Doc

This is the working doc for the "Sprint Skill" project: refining the `/sprint` skill itself, using the sprint process on its own development.

## Where the code lives and how it's edited

- Repo: github.com/itsfurlong/claude-sprint-skill (public).
- Local clone: `/Users/rich/Documents/Claude/Outputs/SprintSkill/github-package` on Rich's Mac. This is the source of truth.
- All edits go through this local clone only, targeted string replacement, never full-file regeneration. The GitHub connector is read-only in practice here: `create_repository` and `create_or_update_file` both return `403 Resource not accessible by integration`. Fine for read-only checks (file contents, log, diffs), never for writes.
- Claude never pushes. Every local commit gets an immediate, unprompted terminal handoff (cd to this absolute path, then git status/log/pull/push) for Rich to run himself.
- A container-side scratch copy exists during active editing sessions, purely as a staging draft before it's committed to this clone. The clone above is authoritative.

## Stack

Not a running app. One markdown file (`skills/sprint/SKILL.md`) plus a `README.md`, versioned as a skill usable two ways: a saved Cowork/claude.ai account skill, and a droppable `.claude/skills/sprint/` folder for Claude Code. Three copies exist and must be kept in sync on every edit:

1. The session's working draft (container workspace, transient).
2. `github-package/skills/sprint/SKILL.md` here — git-tracked, pushed to GitHub.
3. The saved Cowork account skill, updated via the skill-proposal tool. Its synced cache must be read in full immediately before every proposed update, then the complete file is proposed back.

## Testing / verification

No compiler, no test suite. "Done" for a SKILL.md change means: all three copies match, the markdown is internally consistent (numbered steps, cross-references between sections resolve to the right step), and for any behavioral change, Rich has actually invoked `/sprint` and confirmed the model follows the new instruction in practice. Prose-level instructions (e.g. "stop before executing step 1") are not hard gates. A diff looking right is not the same as the behavior holding up in a real run.

## Known fragile areas

- Three-copy sync (draft / github-package / account-skill). Easy to update one and miss another. Update and verify all three in the same pass, every time.
- Cross-references between numbered sections drift when steps get renumbered (a step pointing to "step 7" after the guide was restructured to have that content at step 8, for example). Sweep for stale references after any renumbering.
- Discipline rules like "stop before executing step 1" are enforced by prose, not a mechanical checkpoint. This has already failed once in practice and been patched with stronger wording; if it recurs, the next escalation is a literal question the user must answer, not just stronger phrasing.
- Known stale reference, unfixed as of 2026-09-26: step 1 of SKILL.md says "an empty changelog section ready for step 7" — the changelog step is now numbered 8.

## Changelog

### 2026-09-26
- Fixed the stale cross-reference in SKILL.md step 1 ("ready for step 7" -> "ready for step 8"), matching the current numbering after the earlier restructure that split the stop-before-step-1 rule into its own step. Applied to all three copies (github-package clone, saved Cowork account skill via propose_skills). Committed as 90de4ec, pushed pending. Verified by direct text comparison across copies, no test suite exists for this project.
- Open: whether the "stop before executing step 1" restructuring (commit 721e12d) actually holds in real use is still unconfirmed. Flagged as P1 verification, not yet resolved.
