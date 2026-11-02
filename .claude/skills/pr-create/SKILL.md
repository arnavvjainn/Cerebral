---
name: pr-create
description: Open a PR for the current branch, with tests and blast radius.
---

# Create a pull request

Open a GitHub pull request for the current work using the format below. Use `gh` for all GitHub operations.

## 1. Get the branch ready

1. Find the default branch: `gh repo view --json defaultBranchRef -q .defaultBranchRef.name`.
2. If there are uncommitted changes, show them (`git status --short`) and ask whether to commit them first. Never commit without the user's go-ahead.
3. If the current branch **is** the default branch, create a new branch with a short, descriptive, kebab-case name (e.g. `fix-nav-links`). Commits that are ahead of the remote default branch move with it; afterwards reset the local default branch to match the remote.
4. If the branch has no commits ahead of the default branch, stop and say there is nothing to open a PR for.
5. Collect the change: `git log --oneline <default>..HEAD` and `git diff <default>...HEAD`.

## 2. Test with a subagent

Launch **one** subagent (Agent tool, `general-purpose`) to work out the best way to test this change and run it. Give it the base branch, the commit list, and the list of changed files, and use this brief:

> Determine the most effective way to verify this change and run it. Prefer, in order: tests that directly cover the changed code; the project's existing test suite; a build, typecheck, or lint; actually running the app and exercising the changed behavior. Look at the repo (package.json, Makefile, CI workflows, README, config files) to find the right commands. Do not commit, push, or modify tracked files; you may create throwaway files in a temp directory.
>
> Reply in exactly this format:
> TESTED: yes | no
> RESULT: <one line: what was run and whether it passed, with counts if there are any>
> FAILURES: <short failure details, or "none">
> BLAST RADIUS: <one line: what this change could affect if it is wrong>

Wait for the subagent's result. If **any test failed**, show the failures and ask whether to fix them first or open the PR anyway. Do not open the PR silently with failing tests.

## 3. Write the PR

**Title:** 3–4 words maximum. Sentence case, imperative, no type prefix, no trailing period. Examples: `Fix nav links`, `Add dark mode toggle`, `Remove unused CSS`.

**Body:** exactly this shape:

```
## What it does
<One line describing what this PR does.>

### Tests
<subagent RESULT line>

### Blast radius
<subagent BLAST RADIUS line>
```

If the subagent reported `TESTED: no`, put `Not tested: <one-line reason>` under `### Tests` and keep the blast radius section. That one line is the whole explanation; don't pad it.

If the session has attribution instructions for PR descriptions, append them after the body.

## 4. Open it

1. Push the branch: `git push -u origin <branch>`.
2. Create the PR: `gh pr create --base <default> --title "<title>" --body "<body>"`. Pass the body through a heredoc so the formatting survives.
3. Reply with the PR URL, the title, and the test result in one or two lines.

Don't merge the PR. Merging is the user's call.
