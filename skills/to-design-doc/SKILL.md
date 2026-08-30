---
name: to-design-doc
description: >-
  Create or update a design document when the user asks to turn a discussion
  into a design doc, write down a project design, or invokes $to-design-doc.
  Use for feature, behavior, subsystem, or architectural design that captures
  what the project should do and why. Do not use for ordinary brainstorming,
  project-level PRDs, external research or reference docs, current architecture
  docs, implementation plans, or task tracking.
---

# To Design Doc

Create one readable save point for the design the user asked to preserve. The
document should let the user resume tomorrow or much later without reconstructing
the conversation. Keep the momentum of a side project; do not turn capture into
an interview or specification process.

## Establish Context

Read the repository instructions, `docs/PRD.md` when present, relevant current
documents, relevant research, and the smallest code surface needed to avoid
contradicting the project. Work without requiring any setup skill or repository
configuration.

When the central design depends on an unresolved external fact, verify that fact
before drafting. When the fact would not change the design, preserve it as an
open question instead. Supporting research stays inside the requested design
document; create exactly one primary artifact per invocation.

## Choose the Save Point

Identify the current idea's center of gravity: the single design question the
document should make easy to resume.

- Update an existing document when the new thinking changes or clarifies its
  central answer.
- Create a focused companion document when the thinking answers a separate
  question that can evolve or be revisited independently.
- Recommend no document when the idea contains no durable reasoning worth
  preserving. This is a recommendation only; wait for the user's choice and
  write the document when they still want it.

Topic overlap alone does not determine the boundary. A highlight anchor design
and a highlight appearance design can deserve separate documents even though
both concern highlights.

## Propose Before Writing

Show one compact recommendation before editing:

```text
Recommendation: Create, update, archive, or skip
Path: docs/design/<descriptive-name>.md
Center of gravity: <the design question>
Related changes: <cross-links, PRD update, archive move, or AGENTS pointer>
Open questions: <important unknowns, or none>
```

Wait for approval once. Ask an additional question only when different answers
would produce fundamentally different central designs. Otherwise preserve the
unknowns in the document. Accept "write it down as an open question" and
continue immediately.

When the design conflicts with `docs/PRD.md`, show the conflict and the proposed
PRD change in the approval preview. Approval covers both edits. Do not silently
change the project north star.

`docs/ARCHITECTURE.md` describes the current system. A proposed architectural
change belongs in a focused design document; the implementer updates the current
architecture after the code makes the design true.

## Write the Document

Write new design documents to `docs/design/` with lowercase kebab-case names.
Use [the canonical template](assets/DESIGN_DOC.md). Existing documents keep
their paths unless the approved change explicitly archives them.

The required spine is:

- a descriptive title;
- the original creation `Date` in `YYYY-MM-DD` format;
- a `TL;DR` of two to four sentences; and
- `Design`, with descriptive subsections when useful.

Keep the original date during ordinary updates. When an existing design doc has
no date, recover its initial date from Git history. If no history is available,
use the date it first enters this convention. Add `Open questions`, `Links`, or
another focused section only when it improves this document. `Links` can contain
project documents, official documentation, repositories, issues, or other useful
sources.

Present one solution directly when one solution is enough. When multiple
solutions remain useful to compare, use descriptive option headings and mark
the current preference wherever it appears:

```markdown
### Option A: Native color identity

### Option B: Provider-owned values **[Preferred]**

### Option C: Arbitrary RGB values
```

Mark no option when the discussion has no current preference. Preserve rejected
alternatives only when their reasoning still explains a non-obvious choice.

State a boundary beside the relevant design when omitting it would create a
likely misreading. Avoid routine goal and non-goal sections. Include diagrams,
contracts, schemas, state machines, or small snippets when they preserve a
decision more precisely than prose.

Keep implementation phases, slices, task lists, file inventories, success
criteria, and testing plans out of the design document. Do not add status,
author, or last-updated metadata.

## Maintain Relationships

Add a cross-link when it prevents conflicting sources of truth or makes a
necessary dependency discoverable. Add an `AGENTS.md` pointer when future design
or implementation work would otherwise risk violating the document's durable
decisions.

When an approved design fully replaces an older design, move the older document
to `docs/archive/design/`, preserve its filename and content, and add this notice
below its title:

```markdown
> **Archived:** Superseded by [Replacement](../../design/replacement.md).
```

Update repository links and agent pointers during the same change. A companion
document with a separate center of gravity does not supersede its related doc.

## Finish

Read the finished document as a future resumption point. It is complete when the
current design, important reasoning, unresolved questions, and useful
relationships are clear without the conversation. Remove empty sections and
filler. Stop after the approved document and supporting links; do not create an
issue or implementation plan.
