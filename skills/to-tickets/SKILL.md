---
name: to-tickets
description: Publish an agreed implementation breakdown as resumable GitHub issues. Use only when the user explicitly requests ticket creation.
---

# To Tickets

Preserve agreed work as GitHub issues so it can be resumed in a fresh session.
Invocation authorizes preparation, not immediate publication or implementation.

## Establish the Agreement

Read the supplied plan, conversation, issue, or design reference and applicable
project instructions. Inspect relevant code to check that the agreement still
matches the current system.

When implementation planning or tracking boundaries are missing or materially
stale, use `planning-and-task-breakdown` to establish or revise them with the user.
Reuse an adequate approved plan without repeating its interview. Publication
preparation does not independently redesign tasks.

This step is complete when the task definitions, dependencies, issue grouping,
and PR grouping are agreed and their source context is available.

## Prepare the Issue Set

Resolve the GitHub repository from the explicit request, project tracking
instructions, or repository context, in that order. Confirm it using `gh`.
Ask when ambiguous; setup is not required. This invocation does not persist a
new project default. Inspect related existing issues, including closed matches,
before proposing new ones.

Prepare the agreed work issues, preserving fully defined internal task sections.
Each task keeps its goal and exclusions, implementation approach, rationale, and
definition of done. Preserve approved technical contracts, snippets, and diagrams,
not just outcome summaries. Give each internal task a Markdown checkbox (`- [ ]`)
linked to its detailed task section. New unfinished tasks start unchecked;
preserve verified progress when reusing issues.

Include an implementation handoff in each work issue: when implementing its
tasks, use the Record Task Progress procedure in `planning-and-task-breakdown`
to keep this checklist current. Also record:

- **Source:** durable links to relevant design or planning artifacts.
- **Blocked by:** actual prerequisite issues or internal tasks, or none.
- **PR grouping:** the intended delivery and shared branch context when known.

For multiple work issues, prepare or explicitly reuse a parent containing the
delivery goal, source-design link, shared decisions, and a linked breakdown.
Give every work issue a parent link. A single work issue needs no new parent;
preserve an existing source-parent relationship when applicable.

Link shared design reasoning instead of copying it. If it exists only in chat,
preserve shared implementation decisions in the parent, or the single work issue.
Each task's body and links must let a fresh agent implement without reconstructing
the conversation. A local plan index is unnecessary.

The parent groups the effort, blockers order tasks, and PR grouping identifies
what ships together. Keep internal dependencies as task references; do not create
extra issues just to represent them as native links. Future ideas outside the
approved delivery stay in source documents.

This step is complete when every task is implementation-ready from its body and
links, existing matches are identified, and the entry issue exposes the breakdown.

## Approve and Publish

Show the target repository, complete proposed issue bodies, parent grouping,
blockers, PR grouping, and existing issues to reuse. Include changes to existing
parents and relationships in the preview. Obtain one publication approval,
reusing already settled planning decisions.

Use `gh` to create the parent if needed, then work issues in dependency order.
Attach work issues as native sub-issues and add native blocking relationships
between issues, separately from parent membership. Keep readable parent,
breakdown, and blocker links in bodies. Complete forward links after IDs exist.

For relationship operations, use `gh api` with GitHub's
[sub-issue endpoints](https://docs.github.com/en/rest/issues/sub-issues) and
[dependency endpoints](https://docs.github.com/en/rest/issues/issue-dependencies).
Read the relevant endpoint parameters before mutation: issue numbers used in
paths differ from database IDs required in relationship bodies. Inspect existing
relationships first. Reparenting needs explicit approval; do not force it.

If a native relationship cannot be established, report that publication as
incomplete instead of silently treating a body link as equivalent. Preserve
successful creations. After an uncertain response or failure, reconcile actual
issues and relationships before retrying rather than replaying the batch.

Apply only previewed changes. Leave unrelated issues, labels, and project
configuration unchanged. Publishing never closes a parent or source issue.

## Finish

Read back issue content, parent/sub-issue relationships, blocking relationships,
and body links. Check the approved set is published or accounted for by reused
issues, and report any incomplete publication.

Return the entry issue and published or reused work-issue links. To identify the
next actionable task, use the resume procedure in `planning-and-task-breakdown`;
report unverified prerequisites rather than inventing readiness. This does not
reopen approved design decisions or authorize implementation.

Stop after publication and the next-task recommendation. Do not create PRs or
mark the source design implemented.
