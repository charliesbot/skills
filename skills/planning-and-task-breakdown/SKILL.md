---
name: planning-and-task-breakdown
description: >-
  Collaboratively plan non-trivial implementation and break it into bounded
  tasks. Use before non-trivial coding, when asked to turn a design into an
  implementation plan, when an existing plan needs revision, or when asked
  to resume tracked work or select the next task.
---

# Planning and Task Breakdown

Turn the requested change into an implementation agreement the user can review
before coding. Work from a conversation, design, issue, or clear requirements;
a separate spec is not a prerequisite. The output is a conversational plan,
unless the user explicitly requests a durable artifact.

## Resume Tracked Work

When asked to resume or select a next task, resolve the GitHub repository from
the explicit request, project tracking instructions, or repository context.
Ask if the destination is ambiguous. Read a supplied work issue and its parent.
Without an issue reference, discover open efforts and ask the user to choose
when several are plausible.

Read the effort's goal, shared decisions, linked design, work issues, and
internal task sections. Inspect comments, completion evidence, blockers,
related PRs, and relevant branch state before deciding what remains. Follow
the approved agreement rather than starting a new planning interview.

Recommend one unfinished task whose prerequisites are verified, stating its
outcome and required branch or PR context. A prerequisite verified on a shared
PR branch need not be merged, but its implementation must be available in the
proposed working context. An open issue may already have work underway; a
closed issue alone does not prove its acceptance criteria were met. Report
unknown prerequisite state as unverified rather than ready. If no task is
actionable, report the blocker or that no unfinished task remains.

For a next-task request, stop at the recommendation. For a request to implement,
continue under the project's approval and execution gates, using the steps below
only to resolve missing or changed planning. Material contradictions return to
the user rather than silently replacing the agreement.

## Ground the Goal

Read applicable project instructions, relevant source artifacts, and the smallest
code surface needed to understand existing responsibilities, patterns, and tests.
Treat existing artifacts as sources of truth and link to their reasoning.

Identify the requested outcome, exclusions, current implementation, and risks.
For a broad design, establish which outcomes belong to this delivery rather than
assuming the entire document is in scope. Reuse adequate existing planning and
inspect what has changed since it was agreed.

This step is complete when the delivery goal is clear and remaining unknowns
are identified. Resolve discoverable facts through inspection before asking.

## Agree on the Approach

Discuss consequential decisions with the user: responsibility and module
boundaries, important interfaces, state or data flow, and patterns that materially
affect implementation or review. Recommend an approach with its rationale.
Compare alternatives only when the choice matters, not to fill a template.

Explain how the solution works technically, including integration points and
failure behavior that materially affect it. Use signatures, focused snippets,
schemas, data-flow diagrams, or state diagrams where they make decisions clearer
than prose. Choose the representation for the decision, not to fill sections.

Resolve implementation-shaping unknowns before approval. If a necessary fact
cannot be established, name the investigation needed and keep affected work
unready. The next agent should implement, not choose an architecture, invent an
important contract, or resolve ambiguous behavior. Private helper names and
ordinary code organization remain implementation details.

This step is complete when the user has had an opportunity to shape the approach
and no implementation-blocking design choice remains hidden inside a task.

## Define Tasks and Deliveries

For each task, establish:

- **Goal:** the concrete outcome and exclusions.
- **Implementation approach:** the responsibilities, interfaces, and existing
  patterns that govern the change.
- **Rationale:** task-specific decisions and links to shared design reasoning.
- **Definition of done:** observable acceptance criteria and focused verification,
  including production integration where applicable.

Acceptance criteria establish the task's outcome; the project's verification and
review gates still establish delivery quality. Derive checks from the repository
instead of guessing commands.

Identify genuine dependencies and group tasks into proposed PRs using the
project's PR structure rules. Tasks are implementation checkpoints, not necessarily
merge boundaries. One coherent PR may contain several tasks. Prefer one task
when further decomposition adds no useful boundary.

Choose boundaries by responsibility and verifiable outcome. File counts, line
counts, and context-window estimates are forecasts, not splitting thresholds.
Settle consequential implementation decisions for every task while leaving
ordinary code details to the implementer.

This step is complete when every task has a bounded outcome and finish line,
and its dependencies and PR grouping are explicit.

## Choose Tracking Boundaries

When ticket publication is requested or the effort is already tracked, apply
this section. Otherwise, keep conversational task boundaries and proceed to
Check Coverage and Approve without designing an issue structure.

Agree on the tracking structure without publishing it. Keep internal checkpoints
as fully defined task sections inside one work issue. Give a work unit its own
issue when it should be resumed and tracked independently, not merely because
it is a coding step.

For several related work issues, use a parent holding the delivery goal, source
design link, shared decisions, and breakdown. A single work issue needs no parent.
Link shared decisions instead of repeating them in each task. Preserve important
snippets and diagrams as part of the implementation agreement.

Parent membership groups an effort; blockers order execution; PR grouping
defines what ships together. Several issues may share one coherent PR. Neither
issue count nor PR size should erase task detail or force artificial boundaries.
Keep future possibilities outside the approved delivery's tickets.

This step is complete when each task's home is clear and every work issue is an
independently resumable unit with the context needed to implement it.

## Check Coverage and Approve

Compare the breakdown against the requested scope. Account for every intended
outcome with a task or an explicit deferral. Check that proposed deliveries
preserve the agreed architecture and that dependencies reflect real blockers.

Present the goal, agreed approach, tasks, PR grouping, risks, and verification
in a plan proportional to the work. Discuss material gaps with the user, then
obtain approval under the project's planning gate. Reuse prior approvals for
unchanged decisions.

The plan is complete when approved, coverage is accounted for, and each task
with its linked context is implementation-ready, with no unresolved
implementation-shaping decision. During later implementation, ordinary code
details remain the implementer's responsibility;
material changes return through the project's escalation gate.

Stop at planning when planning is the requested output. This skill creates no
issues or implementation changes, and requires no plan file or downstream skill.
