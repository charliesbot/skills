# Agent Instructions

## Hard Rules

- NEVER use hacks to bypass the type system or linters (e.g., `// @ts-ignore`, suppressing linter warnings) unless explicitly directed.
- Before committing or pushing, verify no secrets are included. NEVER commit `.env` files or expose API keys, tokens, or secrets in any output.
- Bug fixes follow TDD red-green: write a failing test first (red), then implement the fix (green).

## Version Control

Use Git for local version control, Worktrunk for worktree management, and `gh` for GitHub and pull request operations.

- Work in the current checkout by default. Create a worktree only when explicitly requested by the user, not from inferred concurrent activity. For those requests, follow User-Requested Worktrees below.
- Use `git status`, `git diff`, `git log`, and `git show` for inspection.
- Before editing or switching branches, inspect the current branch and uncommitted changes. Preserve existing changes and ask if they conflict with the requested task. Never switch away from an unrelated active task automatically.
- For trivial work, stay on `main` when already there. For non-trivial work, create a feature branch in the current checkout after plan approval. Create independent feature branches from the intended PR base with `git switch -c <branch> <base>`. Inspect ancestry, not only uncommitted changes, before choosing the starting point. Reuse an existing feature branch only when it belongs to the same task.
- Keep commits scoped to the approved change.
- Continue merging through GitHub; do not use `wt merge`.

### PR Structure and Stacks

- Prefer one complete, coherent PR. Split only when every PR is independently useful, production-quality, and consistent with the intended final architecture. If all later PRs were cancelled, each earlier PR must still be worth keeping.
- Use ordinary PRs by default. Use a stack only when two or more approved slices form a strict dependency chain and work must continue before lower PRs merge; use separate branches and PRs for independent changes.
- For a stack, start on the bottom branch, run `gh stack init <bottom-branch>`, then use `gh stack add <branch>` and stack navigation in the same checkout.
- Run stack commands non-interactively: `gh stack submit --auto --open`, `gh stack view --json`, and `gh stack merge <stack-or-pr> --yes --squash`. Never merge a stacked PR with `gh pr merge`.
- After merging a stack, synchronize it with `gh stack sync --prune`.

### User-Requested Worktrees

- Use Worktrunk exclusively to create, switch, list, and remove worktrees. Never use native `git worktree` commands or manually delete worktree directories. If Worktrunk is unavailable or fails, stop and notify the user instead of falling back.
- Run `wt list` before worktree operations. Use `wt switch --create <branch>` for a new task and `wt switch <branch>` only for an existing worktree assigned to that task.
- Use one worktree per independent PR or per complete PR stack, not one per stack layer. Remove finished worktrees with `wt remove` after merging.

## Purpose and Priorities

These instructions are for Charlie's personal side projects. Optimize for
creative momentum: ship useful apps that remain easy for one person to
understand, resume, and change.

Treat correctness, readable code, and coherent architecture as baseline
requirements, not optional polish. Prefer the simplest architecturally sound
solution, not the fastest patch or smallest diff. Simplicity means fewer
concepts to understand and clearer responsibilities, not necessarily fewer
files, layers, or lines.

Give real concepts and responsibilities clear homes. Use appropriate patterns,
abstractions, and boundaries when their benefits in clarity, isolation, testing,
or change justify their complexity. A meaningful boundary can justify an
abstraction without multiple implementations or callers. Do not bypass sound
design merely because a shortcut is faster to implement.

Build for current requirements and established project architecture. Avoid
speculative flexibility, hypothetical scale, and reusable frameworks without
a concrete need. Avoid ad hoc exceptions and temporary workarounds that
undermine the design.

Optimize for sustained velocity, not minimum implementation time. Keep scope
focused and process proportional to risk. Reduce features and ceremony before
compromising code quality. Once the requirements are met and no material
defects or maintainability issues remain, ship rather than keep refining a
good solution because alternatives exist.

## Communication

- Be direct and concise. No preamble, no filler affirmations, no trailing summaries.
- Give opinionated recommendations. Limit options to 2–3 max. No unsolicited alternatives unless they fix a bug, security issue, or significant performance problem.
- Skip explanations of language fundamentals, design patterns, and standard library usage. Do explain project-specific conventions and non-obvious architectural decisions.
- Prefer prose over bullet points unless structure genuinely helps.

## Workflow

Main session handles implementation. Delegate only for the Review Loop below, when explicitly requested by the user, or when a required skill explicitly asks for a subagent.

Non-trivial: new features, refactors, cross-module changes, anything touching auth, payments, or data flow.
Trivial: typos, one-line bug fixes, renames, comment or doc edits, AGENTS.md tweaks, dependency version bumps.

Non-trivial criteria take precedence over trivial examples, regardless of changed line count.

Trivial work can proceed directly. For non-trivial work, follow Plan and Implement below. Before publishing any change, follow Verify and Publish; non-trivial code changes also require the Review Loop.

### Plan

Before non-trivial implementation, inspect the relevant code and present a concise plan covering intended behavior, approach, likely scope, risks or open questions, and verification. Keep the plan proportional to the change and wait for approval once.

Expected files and estimated size are forecasts, not hard boundaries. Use snippets or diagrams only when they clarify a non-obvious interface or architectural decision.

The approved plan is the lightweight spec. Create a separate feature spec only when the feature is ambiguous, high-risk, product-defining, or likely to span multiple sessions.

### Implement

After approval, implement the agreed behavior continuously. Reasonable supporting changes are included when required to complete it. Never introduce temporary abstractions, compatibility layers, duplicate implementations, disabled production paths, or throwaway APIs solely to make a change smaller.

Stop and ask only when intended behavior becomes ambiguous, the architecture or risk changes materially, or the work expands into an unrelated subsystem. File count or changed LOC alone is not a reason to stop.

Keep refactors within the approved scope. Log useful adjacent cleanup as follow-up work or propose a separate cleanup PR. Use `code-simplification` only for separate cleanup PRs or explicitly requested follow-up.

When stuck, try 2–3 approaches before asking. If still blocked, ask with context on what you tried.

### Verify

Run the smallest relevant automated checks and inspect the complete diff. If applicable verification cannot run or fails, report that status and wait before publishing. Perform a deep review when the user explicitly asks.

### Review Loop

After completing non-trivial code changes and passing applicable verification, invoke `reviewer`. The reviewer owns review criteria; the main agent owns fixes and verification. Trivial changes require this loop only when requested.

1. Supply the approved requirements, applicable project instructions, complete task diff boundary, and current verification results. Pause edits to the reviewed code while the reviewer runs.
2. If the verdict is **Ready for human review**, finish the loop. Otherwise, address valid findings or missing review inputs and rerun affected checks. Supply evidence for disputed findings rather than silently dismissing them. Apply the escalation conditions in Implement if a fix changes scope, behavior, architecture, or risk.
3. Repeat the review with a fresh `reviewer`, supplying the same task context plus prior findings, fixes or rebuttals, updated code, and current verification results.

If the reviewer is unavailable or a review blocker cannot be resolved within the approved scope, report what is needed and wait. Self-review does not replace this gate. Declare readiness only after the required loop finishes.

### Publish

Approval to implement a non-trivial change includes committing, pushing, and opening its ready-for-review PR only after applicable verification passes in the current session and any required Review Loop finishes. This gate applies to `git commit`, `git push`, `gh pr create`, and `gh stack submit`. Each stack layer requires approved scope and applicable verification and review.

Changes affecting verified or reviewed behavior invalidate the affected results. Rerun the required checks and review before publishing.

Commit or push trivial changes on `main` only when explicitly asked. Open draft PRs only when explicitly asked.

## Tooling

- GitHub username: charliesbot
- When running inside Herdr (`HERDR_ENV=1`), prefer `herdr` panes for long-running commands, logs, dev servers, watchers, and sibling agents so output stays visible and persistent. Use normal command execution for quick one-shot commands.
