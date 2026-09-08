---
name: to-tickets
description: Publish an agreed implementation breakdown as resumable GitHub issues.
disable-model-invocation: true
---

# To Tickets

Preserve agreed work as GitHub issues so it can be resumed in a fresh session.
Invocation authorizes preparation, not immediate publication or implementation.

## Establish the Agreement

Read the supplied plan, conversation, issue, or design reference and applicable
project instructions. Inspect relevant code to check that the agreement still
matches the current system.

When implementation planning is missing or materially stale, use
`planning-and-task-breakdown` to establish or revise it with the user. Reuse an
adequate approved plan without repeating its interview. Publication preparation
does not independently redesign tasks.

This step is complete when the task definitions, dependencies, and PR grouping
are agreed and their source context is available.

## Prepare the Issue Set

Confirm the target repository from project context and GitHub information using
`gh`. Ask only if the destination remains ambiguous. Inspect related existing
issues, including closed matches, before proposing new ones.

Prepare one issue per agreed task. Preserve the plan's goal, implementation
approach, rationale, and definition of done, plus:

- **Source:** durable links to relevant design or planning artifacts.
- **Dependencies:** the tasks that actually block this task, or none.
- **PR grouping:** which tasks belong to the same intended delivery.

Link shared design reasoning instead of copying it. When the plan exists only
in conversation, put the task-specific agreement in the issue so a new session
does not need the chat. Preserve shared decisions without a durable home in one
relevant issue and link that context from the others; a context link alone is
not a blocking dependency. A separate plan file or parent issue is not required.

An issue must make clear what can be implemented and how completion is verified.
Multiple issues can share one PR; issue boundaries do not authorize separate PRs.

This step is complete when every proposed issue can be resumed from its body and
links, and existing matches are identified rather than silently duplicated.

## Approve and Publish

Show the target repository, proposed issue bodies, dependencies, PR grouping,
and any existing issues to reuse. State that approval publishes this issue set.
Obtain one publication approval, reusing already settled planning decisions.
Material changes to existing issues require inclusion in that preview.

Use `gh` to publish approved new issues in dependency order so blockers can be
referenced by real URLs. Record dependency and PR-group links in the issue bodies;
complete forward links after the relevant issues exist. Leave unrelated issues,
labels, and project configuration unchanged.

Track each successful issue creation. If publication fails or a response is
uncertain, reconcile the repository's actual issues before retrying. Stop and
report partial results when the failure cannot be resolved safely. Preserve
successful creations rather than deleting them or replaying the entire batch.

## Finish

Read back the published issues and verify their agreed content and links.
Return their URLs and any remaining publication work.

Completion means the approved issue set is published or explicitly accounted
for by reused issues, with correct dependencies and PR grouping. Stop here:
ticket approval does not start implementation, create PRs, close source issues,
or mark the source design implemented.
