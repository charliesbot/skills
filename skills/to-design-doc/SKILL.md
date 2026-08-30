---
name: to-design-doc
description: >-
  Create or update a resumable design document when the user explicitly
  requests one for a feature, behavior, subsystem, or architectural change.
---

# To Design Doc

Create one readable save point for the design the user asked to preserve. The
document should let the user resume tomorrow or much later without reconstructing
the conversation. Keep the momentum of a side project; do not turn capture into
an interview or specification process. Match the document's depth to the
complexity of the design question, not the length of the conversation.

## Establish Context

Read applicable `AGENTS.md` and `CLAUDE.md` files when present, `docs/PRD.md`
when present, relevant current documents, relevant research, and the smallest
code surface needed to avoid contradicting the project. Work without requiring
any setup skill or repository configuration.

When the central design depends on an unresolved external fact, verify that fact
before drafting. When the fact would not change the design, preserve it as an
open question instead. Supporting research stays inside the requested design
document. The design document is the one primary artifact per invocation.
Approved PRD changes, agent-instruction pointers, cross-links, and archive moves
are supporting edits, not additional primary artifacts.

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
Path: docs/design/<descriptive-name>.md, or none for skip
Center of gravity: <the design question>
Related changes: <cross-links, PRD update, archive move, or AGENTS pointer>
Open questions: <important unknowns, or none>
```

Use `Path: none` for a skip recommendation.

Wait for approval once. Ask an additional question only when different answers
would produce fundamentally different central designs. Otherwise preserve the
unknowns in the document. Accept "write it down as an open question" and
continue immediately.

When the design conflicts with `docs/PRD.md`, show the conflict and the proposed
PRD change in the approval preview. Approval covers both edits. Do not silently
change the project north star.

When present, `docs/ARCHITECTURE.md` describes the current system. A proposed
architectural change belongs in a focused design document; the implementer
updates the current architecture after the code makes the design true.

## Write the Document

Write new design documents to `docs/design/` with lowercase kebab-case names.
Use [the canonical template](assets/DESIGN_DOC.md). Existing documents keep
their paths unless the approved change explicitly archives them.

Name a companion document for its own center of gravity. Reuse a domain prefix
when it improves recognition, such as `highlight-anchors.md` and
`highlight-appearance.md`.

Keep the original date during ordinary updates. When an existing design doc has
no date, recover its initial date from Git history. If no history is available,
use the date it first enters this convention.

Keep an existing design document as a clean snapshot of the current design.
Integrate changes into the relevant sections and rely on Git for history instead
of appending dated update sections or changelogs.

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
likely misreading. Avoid routine goal and non-goal sections.

Keep implementation phases, slices, task lists, file inventories, success
criteria, and testing plans out of the design document. Do not add status,
author, or last-updated metadata.

## Maintain Relationships

Add a cross-link when it prevents conflicting sources of truth or makes a
necessary dependency discoverable. Add an `AGENTS.md` pointer when future design
or implementation work would otherwise risk violating the document's durable
decisions.

When an approved design fully replaces an older design, move the older document
to `docs/archive/design/`, creating that directory when the first approved
archive move needs it. Preserve the archived document's filename and content,
and add this notice below its title:

```markdown
> **Archived:** Superseded by [Replacement](../../design/replacement.md).
```

Update repository links and agent pointers during the same change. A companion
document with a separate center of gravity does not supersede its related doc.

## Finish

The save point is complete when the document answers the approved center of
gravity, every approved related change is applied, and every important unknown
is resolved or preserved. Remove empty sections and filler. Stop after the
approved document and supporting links; do not create an issue or implementation
plan.
