---
name: sprint
description: "Run any multi-part project (code, writing, research, a website, a business plan) as a human-led sprint: find the working doc, triage, lay out what the work requires, write a reviewable guide of paste-ready steps, then execute one step at a time, stopping after each. Never touches the live version or deletes files. Needs file access (Cowork or Claude Code)."
---

# Sprint

A sprint means: sit down and work until this part is finished. This skill turns "here's what I want done" into a written guide the user reviews before any work changes, then a sequence of small, verifiable, stoppable steps. It fixes two failure modes: sessions that produce a lot of work before checking whether the approach is right, and sessions that burn tokens re-reading whole files or re-establishing context that a fresh, bounded prompt would not need.

It works for any project with more than one part: code, a research paper, a website, a business plan, a campaign. The core process below applies to all of them. Code projects add the rules in "Code projects"; other project types take their done check and checkpoint method from the project-type table.

## When to use this

Any project-based request that takes more than one step and produces or changes files: a bug fix, a feature, a draft chapter, a site build, a plan with numbers behind it. Not for single-turn tasks (a quick answer, a one-line change) that need no guide and no verification step.

## Before anything else: check for file access

A sprint reads and writes the project's files and its working doc, so it only runs where Claude has that access (Cowork, Claude Code). If this session has no file tools (a plain chat), reply with this one line and stop, without writing a guide: "The sprint skill needs access to your project's files, so it can't run in this chat. Open the project in Cowork (or Claude Code) and run /sprint there."

## The process

**Mandatory, non-negotiable rule: presenting the guide and executing step 1 are two separate turns.** Writing the guide (steps 1 through 4 below) ends the turn. Step 5 covers this in detail, but state it here too because it is the rule most likely to get skipped: never run step 1, or any part of the work, in the same response that presents the guide to the user, no matter how obvious the fix looks or how the original request was phrased. Stop, show the guide, and wait for the user's next message (in Claude Code, their plan approval; see step 5) before doing anything else.

### 1. Find the working doc, or create one

The project's working doc is the one file that holds its goal, ground rules, and history. Step 3 reads from it; step 8 writes to it.

Look before creating. Most sprints are not the first prompt in a project, so a working doc usually already exists, possibly under another name. Check these in one batch:

1. The project's folder or repo root: WORKING.md, or any file that reads like a brief, plan, or changelog.
2. Docs attached to the project, if the environment has them.
3. Any file that CLAUDE.md, a README, or a project instruction points to.

If one turns up, use it wherever it lives and whatever it's called, and read it: the ground rules, protected items, and history the guide needs already live there. If nothing turns up, ask the user where it is, or whether this is a new project. A new project and a new chat in an old project look the same from inside the session, so never assume.

Create a new doc only when the user confirms none exists. Name it WORKING.md and put it in the project's home (repo root or project folder) unless the user or the project's instructions say otherwise. Ask only for what can't be learned by looking, then write it with:

- Goal: what the project is for and what finished looks like.
- Where the work lives and how it's edited, and what counts as the live version (the deployed site, published doc, sent message, installed skill). The sprint never touches the live version.
- Project type (code, writing/research, website, business, other), plus the done check and checkpoint method that type uses. See the project-type table and "Code projects" below.
- Protected items: anything off-limits or needing extra care, and known-fragile areas.
- Changelog: empty, ready for step 8.

The working doc is private by default: it holds how the work gets made, not the work itself. When it lives inside a git repo, add it to `.gitignore` as part of creating it, unless the user explicitly says to share it. If an existing working doc is already tracked, flag it and offer to untrack it (`git rm --cached`, which keeps the file on disk); don't untrack it without a yes.

A sprint with no working doc has nowhere to read ground rules from and nowhere to write the changelog entry to. It does not function without one.

### 2. Triage first, always

Before writing a single line of the guide, classify what's being asked. For a batch of items (requests, bug reports, feedback, open questions), build a short table: item, type (task / fix / decision / question), priority (P1/P2), and whether it ships this sprint or gets deferred with a one-line reason. Anything that reverses an earlier explicit decision (a scope choice, a design call, an approved direction) gets flagged and needs the user's explicit yes before it goes in the guide, not just a note.

Confirm before producing. Whatever the work rests on (a bug's cause, a source's claim, a market assumption, a number in a plan) gets stated as likely and marked unverified, and the guide's first step confirms it for real. Never write a step that produces work before the thing it depends on is confirmed.

### 3. Ground rules, stated once, up front

Every guide opens with the constraints that apply to every step in it, so no individual step has to repeat them. They come from the working doc, not from assumptions. Where the user's own or the project's instructions conflict with this skill, theirs win; say so in the ground rules so the conflict is visible.

- Where the work lives and how it's edited: the working copy named in the working doc. Every change is a targeted edit to it, never a regeneration of a whole file or document; full rewrites of anything nontrivial have a real history of silently corrupting an unrelated part. Code projects add their own rules here (see "Code projects").
- What's off-limits: the live version (the deployed site, published doc, sent message, installed skill) is never touched during a sprint, and shipping it is always the human's handoff (step 7). Name any protected items from the working doc that need extra care.
- Never delete files. A file recommended for deletion gets moved into a `_recommended-for-deletion/` folder in the project's home, and the user keeps or discards it. Any other irreversible action (overwriting without a copy, rewriting history, sending, spending) needs the user's explicit yes for that specific action.
- What "done" means: the done check for this project type, from the working doc. Every step that produces work ends with that check built into its own prompt. After a checkpoint, give the handoff (step 7) and stop.
- Anything that must never appear in a file, a doc, a commit, or a message (secrets, personal or customer data, a private link).

### 4. Say what this requires, then break the work into phases and bounded steps

Before any steps, the guide answers one question: what does doing this even require? Many requests hide prerequisites the user hasn't thought about, and the user should see them before approving a plan, not discover them at step 6. Give a short block, right after the ground rules:

- What the user needs to provide: inputs, files, content, or facts only they have.
- Decisions that are theirs to make, and which step each one blocks.
- Access or tools the work needs (accounts, credentials, software, budget), and whether this session has them.
- Unknowns: what's assumed and unverified, and which step confirms it.
- Verdict and size, in one line: go, needs clarification (name what's missing and which step it blocks), or don't do this (say why in one sentence), plus how many phases and steps, so the user can judge whether this is one sprint or several. Small, clear asks default to go without discussion.

If the answer changes the request (a missing input, a decision that has to come first, a scope that's bigger than it sounded), say so plainly. The user may reshape the ask before any steps get written. A "don't do this" verdict ends the guide there: no steps get written until the user decides.

Group related work into phases (by topic, or by the part of the project touched). Within a phase, each step is small enough to paste into a single fresh prompt and get a complete, checkable result back. A step does one thing: confirm (research, diagnose, check a source), or produce one change, or verify. Don't combine "produce" and "verify" into one step for anything nontrivial: a broken result is easier to catch when verification is its own pass.

Every step gets:

- A one-line statement of its output (what exists after this step that didn't before).
- A single prompt, written as if handed to a fresh session with no memory of this conversation: it names the exact file(s) and section, what to change, what not to touch, and what check to run before reporting back. A prompt that says "fix the part we discussed" is not paste-ready; a prompt that names the file, the section, the expected before and after, and the check to run is.

The last step of every guide is always verify-checkpoint-stop: run the full done check one more time, then do a real end-to-end check (read the whole draft through, load the page, rerun the numbers, run the app; not just the step-level checks). Next, where subagents exist, hand a fresh, read-only subagent with no conversation history only the finished output and the original brief (the request plus the working doc's goal), ask what is wrong, missing, or unclear, and report its findings to the user unfiltered. Where no subagent exists, say so in the report rather than skipping the check silently. If it finds a real problem, stop before the checkpoint and let the user decide. Then make a checkpoint that records what changed and why, and report it. Immediately after the checkpoint, before anything else, give the handoff (step 7). Never wait to be asked for it. Nothing after that until the user has reviewed the work themselves and given the go-ahead to ship.

If a guide makes more than one checkpoint (a multi-topic sprint, or work landed in stages), each one gets its own handoff right after it, not one combined handoff saved for the end.

### 5. Stop after presenting the guide: mandatory

After writing the guide (steps 1 through 4), the response ends there. Do not run the guide's step 1, do not touch any file, do not run any command, even a read-only one, in that same response, and even if the work looks completely obvious or the user's original request sounded like a green light to just do it. A request to fix, build, write, or plan something is a request for a guide, not a request to skip the guide. Show the triage table, the ground rules, what the work requires, and every step's prompt, and wait.

The user needs that pause to read the guide and change anything before the work moves: reorder steps, cut one, reword a prompt, correct a wrong assumption in the ground rules. Only start step 1, in a later turn, once they've explicitly said to proceed: either a plain go-ahead with no changes, or a go-ahead after they've told you what to fix and you've fixed the guide itself.

In Claude Code, make this stop mechanical: once the working doc exists, enter plan mode before presenting the guide, so edits stay blocked until the user approves it. Do this automatically, without asking. In any other environment, don't mention plan mode. This covers only this gate, not the per-step stops in step 6. Approving the plan counts as the go-ahead for step 1 only: run it, then stop as step 6 requires.

### 6. Execute one step at a time

Once the user has given the go-ahead from step 5, run the steps in order. After each step: report what happened, what the check showed, and stop. Do not start the next step without an explicit go-ahead, even if the result was clean. A guide that gets executed end to end with no stops has stopped being a sprint.

If a step's result contradicts an assumption from the ground rules or an earlier step, stop and say so before continuing, even if the fix is obvious. Silently patching over a wrong assumption is how a guide drifts from what it says it's doing.

Each stop is a checkpoint for both sides. Sometimes the report ends in a plain "continue?", and that's fine. But if the step turned up anything unforeseen (a complication, a risk, or an opportunity), flag it at the stop and let the user decide whether to act on it. Don't fold it into the next step on your own. The user can raise the same kinds of things at any stop, and the guide gets updated before the work moves on.

### 7. Hand off what only the human does: always, automatically

Claude does the work on the working copy; the human ships it. Any action that changes the live version or leaves the project (deploying, pushing, publishing, submitting, sending, spending money) belongs to the human, unless they have explicitly told Claude to take that specific action and Claude actually has the access to do it. This is not a wait-to-be-asked step: every checkpoint a sprint makes gets its handoff right away, unprompted, in the same turn as the checkpoint.

The handoff is written so the user can act on it with no edits:

1. One line per change since the last handoff, saying what it does, so the user isn't shipping blind.
2. Where the checked work is (exact path or link) and what, specifically, to review before shipping.
3. The exact action to take: a copy-paste block for anything run in a terminal, or numbered steps for anything done by hand. Code projects use the git block in "Code projects".

### 8. Update the working doc after: not optional

The working doc from step 1 gets one dated entry after the sprint closes: what shipped, checkpoint references (commit hashes, file versions), what's shipped versus checkpoint-only, what was verified and how, any caveat or unverified claim that shipped anyway, and anything left open. This is what makes the next sprint able to start from ground truth instead of from memory.

## Code projects

A project is a code project when the work lives in a codebase. These rules add to the core process, and where they're more specific, they win.

- Where the code lives: the local clone. Every edit goes through it. A hosted repo connector or API is fine for read-only work (browsing history, diffing a past commit) but never for writing a change, not even a one-line one: most such write endpoints have no partial-patch mode, so even a small edit means resending the whole file. If no local clone exists yet, setting one up is part of the ground rules, before step 1 of the actual work.
- Protected code paths: name anything off-limits or needing extra care (a regex another feature depends on, a canary check, anything that touches money or auth).
- Steps: diagnose, or implement one change, or add tests for one behavior, or verify end to end. The done check is that it compiles and lints clean and the existing test suite still passes, built into every code-touching step's own prompt. The final end-to-end check runs the app, not just the unit tests.
- Checkpoint: a local commit whose message states the root cause and what changed. Claude does not push unless it has been explicitly given push credentials and told to use them. The live version is the remote branch and anything that deploys from it. The working doc is never part of a commit; it stays in `.gitignore` (see step 1).
- Git in a sandbox: run read-only checks as `git --no-optional-locks status` (and `diff`, `log`) so they don't leave lock files behind. If the environment can't delete files, a commit can fail on `.git/index.lock`. Don't retry or work around it: hand the user the commit command to run themselves, and tell them if a stale lock file needs removing first.

The handoff (step 7) for every commit is a single fenced terminal block the user can paste as-is into a fresh terminal window. A fresh terminal opens at the home directory, so the block starts with `cd` to the repo's absolute path from the ground rules, never a relative path:

```bash
cd /absolute/path/to/the/repo
git status
git log origin/BRANCH..HEAD --oneline
git pull --rebase origin BRANCH
git push origin BRANCH
```

Use the real absolute path, and replace BRANCH with the output of `git branch --show-current`; never assume `main`. Right above the block, one line each: how many local commits are ahead of the remote, and what each one does. If a step's own check already ran `git log` or `git status`, reuse that output instead of running it again.

## Project types

The working doc names the project type, and the type sets three things the core process leaves open. A checkpoint without git is a new versioned copy (`name_v2.ext`) saved next to the previous one, never an overwrite; later edits target the new copy.

| Type | Done check | Checkpoint | Human-only handoff |
|---|---|---|---|
| Code | Compiles, lints, tests pass; end-to-end run | Local commit | Push, deploy (see "Code projects") |
| Writing / research | Every claim sourced and every citation resolves; full read-through against the brief for structure and voice | Versioned copy | Submit, publish, send |
| Website | Pages load, links work, layout holds at phone and desktop width, no console errors | Commit if in a repo, otherwise versioned copy | Deploy, publish, point a domain |
| Business | Every number recomputes from its stated inputs; assumptions listed and marked verified or unverified | Versioned copy of the plan or model | Send, file, sign, spend |
| Other | A check the user agrees to in the working doc before step 1 | Versioned copy | Anything that makes it public or can't be undone |

## Token efficiency rules

- Don't re-paste or re-read a whole file or document inside a step's prompt when a targeted edit, or a description of the exact lines or passage to change, will do.
- For a large or complex edit, delegate to a subagent that can read and edit directly, if one is available, rather than pulling the whole file into the main conversation just to hand it back with one part changed.
- Keep each step's prompt self-contained but minimal: state only what that step needs, not a recap of the ground rules or earlier steps.
- Batch independent read-only checks (reading two files, checking two pages, verifying two sources) into one round of tool calls instead of one at a time when there's no dependency between them.
- Prefer one step that confirms cleanly over three that guess and check. A wrong guess costs more than the extra step to confirm first would have.

## Style

Direct, no filler. Numbered steps, not prose paragraphs, for anything the user will act on. Every prompt block is written to be pasted as-is, not edited before use. The user's own style preferences, from their instructions or the working doc, apply on top of this.
