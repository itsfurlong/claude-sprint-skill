---
name: sprint
description: Turn a bug report, feature request, or batch of small fixes for a codebase-backed project into a bounded, step-by-step implementation guide with paste-ready prompts, then execute it one step at a time, stopping for a go-ahead after each step. Use when starting a new round of work on an app where changes should land as local commits and get pushed from the user's own terminal, not by Claude.
---

# Sprint

A sprint turns "here's a bug / here's what I want built" into a written guide the user can review before any code changes, then a sequence of small, verifiable, stoppable steps. It exists to fix two failure modes: sessions that write a lot of code before checking whether the approach is right, and sessions that burn tokens re-reading whole files or re-establishing context that a fresh, bounded prompt would not need.

## When to use this

Any task that will touch a real codebase: a bug fix, a small feature, a batch of related fixes triaged together, a content or copy change that requires code edits. Not for pure research, writing, or one-line trivial changes that need no verification step.

## The process

**Mandatory, non-negotiable rule: presenting the guide and executing step 1 are two separate turns.** Writing the guide (steps 1 through 4 below) ends the turn. Step 5 covers this in detail, but state it here too because it is the rule most likely to get skipped: never run step 1, or any part of the work, in the same response that presents the guide to the user, no matter how obvious the fix looks or how the original request was phrased. Stop, show the guide, and wait for the user's next message before doing anything else.

### 1. Confirm the working doc exists, or create it first

Ground rules can only be "stated once, up front" (step 3) if there's somewhere they were stated before this sprint and will still be stated after it. That place is the project's working doc. Before triage, check whether one exists. If it does, read it: that's where the ground rules, stack details, protected code paths, and history the guide needs already live.

If none exists, this is the first sprint on this project and creating the doc is part of this step, not optional, not deferred. Ask the user only for what isn't yet knowable by looking (repo location, deploy target, anything genuinely outside what the code and conversation already show), then write a working doc with: where the code lives and how it's edited, the stack and services involved, testing setup, any known-fragile areas, and an empty changelog section ready for step 7. A sprint with no working doc has nowhere to read ground rules from and nowhere to write the changelog entry to — it does not function without one.

### 2. Triage first, always

Before writing a single line of the guide, classify what's being asked. For a batch of items (bug reports, feature requests, feedback), build a short table: item, type (bug / feature / content), priority (P1/P2), and whether it ships this sprint or gets deferred with a one-line reason. Anything that reverses an earlier explicit decision (a scope choice, a design call already made) gets flagged and needs the user's explicit yes before it goes in the guide, not just a note.

For a bug: state the likely root cause and mark it unverified. The first step of the guide diagnoses it for real. Never write a fix step before the cause is confirmed.

### 3. Ground rules, stated once, up front

Every guide opens with the constraints that apply to every step in it, so no individual step has to repeat them:

- Where the code actually lives: the local clone. Every edit in the guide goes through it — targeted string replacement, never full-file regeneration. A hosted repo connector or API is fine for read-only work (browsing history, diffing a past commit) but never for writing a change, not even a one-line one: most such write endpoints have no partial-patch mode, so even a small edit means resending the whole file, and full-file rewrites of anything nontrivial have a real history of silently corrupting an unrelated line. If no local clone exists yet, getting one set up is part of the ground rules, before step 1 of the actual work.
- Any code paths that are off-limits or need extra care (a regex another feature depends on, a canary check, anything that touches money or auth).
- What "done" means for a code-touching step: it compiles/lints clean and the existing test suite still passes. Every step that touches code ends with that check built into the step's own prompt, not deferred to a separate step.
- The commit-and-stop rule: Claude commits locally with a message stating root cause and what changed, then immediately hands over a copy-paste terminal block for status, log, and push (see section 7) without being asked, then stops. Claude does not push itself unless it has been explicitly given push credentials and told to use them.
- Anything that must never appear in a commit message, a file, or a doc (secrets, live customer data, a raw link that shouldn't be public).

### 4. Break the work into phases and bounded steps

Group related work into phases (by topic, or by the feature area touched). Within a phase, each step is small enough to paste into a single fresh prompt and get a complete, checkable result back. A step should do one thing: diagnose, or implement one change, or add tests for one behavior, or verify end-to-end. Don't combine "implement" and "verify" into one step for anything nontrivial — a broken implementation is easier to catch when verification is its own pass.

Every step gets:

- A one-line statement of its output (what exists after this step that didn't before).
- A single prompt, written as if handed to a fresh session with no memory of this conversation: it names the exact file(s), the exact function or behavior, what not to touch, and what check to run before reporting back. A prompt that says "fix the bug we discussed" is not paste-ready; a prompt that names the function, the expected before/after, and the test to run is.

The last step of every guide is always verify-commit-stop: run the full test suite one more time, do a real end-to-end check (not just unit tests), commit locally with both the root cause and the fix stated in the message, and report the commit hash. Immediately after that commit, before anything else, give the push handoff described in section 7 — never wait to be asked for it. Nothing after that until the user has tested it themselves and given the go-ahead to push.

If a guide has more than one commit-producing step (a multi-topic sprint, or a fix landed in stages), each one gets its own push handoff right after its commit, not one combined handoff saved for the end.

### 5. Stop after presenting the guide — mandatory

This is its own step, not a footnote to step 6, because it is the rule most likely to get skipped: presenting the guide and starting to execute it are two different go-aheads, and they cannot both happen in the same turn.

After writing the guide (steps 1 through 4), the response ends there. Do not run step 1, do not touch any file, do not run any command, even a read-only one, in that same response, and even if the fix looks completely obvious or the user's original request sounded like a green light to just do it. A request to fix a bug or build a feature is a request for a guide, not a request to skip the guide. Show the triage table, the ground rules, and every step's prompt, and wait.

The user needs that pause to read the guide and change anything before code moves: reorder steps, cut one, reword a prompt, correct a wrong assumption in the ground rules. Only start step 1, in a later turn, once they've explicitly said to proceed — either a plain go-ahead with no changes, or a go-ahead after they've told you what to fix and you've fixed the guide itself.

### 6. Execute one step at a time

Once the user has given the go-ahead from step 5, run the steps in order. After each step: report what happened, what the check showed, and stop. Do not start the next step without an explicit go-ahead, even if the result was clean. This is the actual point of the skill — it is a discipline, not just a document format. A guide that gets executed end to end with no stops has stopped being a sprint.

If a step's result contradicts an assumption from the ground rules or an earlier step, stop and say so before continuing, even if the fix is obvious. Silently patching over a wrong assumption is how a guide drifts from what it says it's doing.

### 7. Hand off the push — always, automatically, no exceptions

Claude does not push to a remote unless explicitly told to and actually has credentials to do so. This is not a wait-to-be-asked step: every single local commit a sprint produces gets its push handoff right away, unprompted, in the same turn as the commit.

The handoff is a single fenced, copy-paste-ready terminal block, formatted so the user can paste it as-is into a fresh terminal window with no edits. That means it cannot assume the user's shell is already sitting in the repo directory — a fresh terminal opens at the user's home directory, so the block starts with `cd` to the repo's absolute path (the one stated in the ground rules, step 3), not a relative path and not an assumption they're already there:

```bash
cd /absolute/path/to/the/repo
git status
git log origin/main..HEAD --oneline
git pull --rebase origin main
git push origin main
```

Use the real absolute path from the ground rules, not the placeholder above. Adjust the branch name to match the repo. Right above the block, in one line each: how many local commits are ahead of the remote, and a one-line description of what each one does, so the user isn't pushing blind. If a step's own prompt already ran `git log`/`git status` as part of its check, reuse that output instead of re-running it just to fill in the block.

This handoff is the default ending of any step that produces a commit, whether that's the guide's final verify-commit-stop step or an earlier step that commits mid-guide. Never make the user ask for it.

### 8. Update the working doc after — not optional

The working doc from step 1 gets one dated entry after the sprint closes: what shipped, commit hashes, what's pushed versus commit-only, what was verified and how, any caveat or unverified claim that shipped anyway, and anything left open. This is not conditional on the project already having a habit of keeping one — step 1 guaranteed the doc exists, so step 8 always has somewhere to write. This is what makes the next sprint able to start from ground truth instead of from memory.

## Token efficiency rules

- Don't re-paste or re-read a whole file inside a step's prompt when a targeted search-and-replace, or a description of the exact lines to change, will do.
- For a large or complex file edit, delegate to a subagent that can read and edit directly, rather than pulling the whole file into the main conversation just to hand it back with one function changed.
- Keep each step's prompt self-contained but minimal: state only what that step needs, not a recap of the ground rules or earlier steps — those already live in the guide and don't need to be repeated inside every prompt.
- Batch independent read-only checks (reading two files, checking two pages, running two independent tests) into one round of tool calls instead of one at a time when there's no dependency between them.
- Prefer one step that diagnoses cleanly over three that guess-and-check. A wrong guess costs more tokens than the extra step to confirm root cause first would have.

## Style

No em dashes. Direct, no filler. Numbered steps, not prose paragraphs, for anything the user will execute. Every prompt block is written to be pasted as-is, not edited before use.
