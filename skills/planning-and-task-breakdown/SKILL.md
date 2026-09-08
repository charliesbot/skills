---
name: planning-and-task-breakdown
description: >-
  Collaboratively plan non-trivial implementation and break it into bounded
  tasks. Use before non-trivial coding, when asked to turn a design into an
  implementation plan, or when an existing plan needs revision.
---

# Planning and Task Breakdown

Turn the requested change into an implementation agreement the user can review
before coding. Work from a conversation, design, issue, or clear requirements;
a separate spec is not a prerequisite. The output is a conversational plan,
unless the user explicitly requests a durable artifact.

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

Resolve questions that would change scope or architecture before final approval.
Exact private methods and file inventories can remain implementation details.
Use snippets or diagrams when they express a non-obvious decision more clearly
than prose.

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
Keep code-sensitive details flexible while settling consequential implementation
decisions for every task.

This step is complete when every task has a bounded outcome and finish line,
and its dependencies and PR grouping are explicit.

## Check Coverage and Approve

Compare the breakdown against the requested scope. Account for every intended
outcome with a task or an explicit deferral. Check that proposed deliveries
preserve the agreed architecture and that dependencies reflect real blockers.

Present the goal, agreed approach, tasks, PR grouping, risks, and verification
in a plan proportional to the work. Discuss material gaps with the user, then
obtain approval under the project's planning gate. Reuse prior approvals for
unchanged decisions.

The plan is complete when approved, coverage is accounted for, and no unresolved
decision would materially change scope or architecture. During later
implementation, ordinary code details remain the implementer's responsibility;
material changes return through the project's escalation gate.

Stop at planning when planning is the requested output. This skill creates no
issues or implementation changes, and requires no plan file or downstream skill.
