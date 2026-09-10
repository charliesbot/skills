---
name: setup-charliesbot-skills
description: Set up Charlie's documentation and GitHub task-tracking conventions in a repository. Use only when the user explicitly requests this setup.
---

# Setup Charliesbot Skills

Make a repository predictable for Charlie's documentation and delivery workflow.
Establish canonical documentation and task-tracking sections in `AGENTS.md`,
and organize existing project knowledge after approval. Work without installing,
detecting, or invoking other skills.

## Inspect the Repository

Read root `AGENTS.md` when present. Treat it as the only repository-instruction
source and exclude it from document classification. Ignore `CLAUDE.md` and do
not create or modify it.

Inspect tracked Markdown files directly at the repository root and throughout
`docs/`. Do not crawl module, dependency, generated, or unrelated documentation
trees. Leave these standard repository files at the root:

- `README.md`
- `CHANGELOG.md`
- `CONTRIBUTING.md`
- `SECURITY.md`
- `LICENSE.md`

Inspect the working tree before proposing changes, but do not require it to be
clean. Identify unrelated changes so setup can leave them untouched.

## Classify Project Documents

Classify each document by its current job, not its filename or headings:

- `docs/PRD.md` defines project direction and the north star.
- `docs/ARCHITECTURE.md` describes the current system.
- `docs/design/` contains focused design reasoning.
- `docs/research/` contains external API, platform, and technical research.
- `docs/archive/` contains non-current documents.
- Other project documents stay directly under `docs/`.

Use lowercase kebab-case filenames except for the fixed `PRD.md` and
`ARCHITECTURE.md` names. Preserve an archived document's lowercase filename when
possible.

Classify a document as archived only when it explicitly says it is superseded or
non-current, a current replacement is clear, or the user confirms that status.
Age alone is not evidence. Setup relocates documents already known or confirmed
to be non-current; it does not decide or record which design supersedes another.

Recommend leaving an ambiguous document in place. Never overwrite an existing
destination or invent a numbered filename. Report the conflict and recommend a
descriptive merge, rename, or no move for the user to decide.

## Establish Task Tracking

Read existing tracking instructions and repository remotes. Use an explicit user
destination first, otherwise preserve a configured GitHub repository or confirm
the repository identified through `gh`. Ask if the destination is ambiguous.
Surface conflicting or non-GitHub tracking instructions for a decision rather
than silently replacing them. If no GitHub repository is available, leave
tracking unresolved and offer documentation-only setup.

Propose a canonical `## Task tracking` section using the confirmed `owner/repo`.
Setup records where tracked work lives and when to load its resume procedure;
the planning and ticket skills own that procedure and publication. Setup creates
no issues or labels and does not migrate an existing backlog.

## Preview Once

Show one combined preview before editing:

```text
AGENTS.md: create or replace Project documentation and Task tracking sections
Task tracking: <GitHub owner/repo, or unresolved and left unchanged>
Classifications: <source -> category, including files left in place>
Moves and renames: <source -> destination, or none>
Removed guidance: <overlapping AGENTS.md guidance, or none>
Link updates: <affected files, or none>
Conflicts: <items needing a decision, or none>
```

Every classification requires confirmation, including archive classifications.
Include each complete proposed `AGENTS.md` section in the preview. Wait for one
approval before editing. Treat the user's corrections plus approval as the final
preview unless they reveal a new conflict or materially change the scope.

## Apply the Approved Setup

Create root `AGENTS.md` when missing. Otherwise replace existing
project-documentation and approved task-tracking sections. Remove previewed
overlapping guidance so each configured topic has one canonical section.
Preserve every unrelated instruction.

Use this exact section:

```markdown
## Project documentation

- `docs/PRD.md`, when present, defines the project direction and north star.
- `docs/ARCHITECTURE.md`, when present, describes the current system.
- `docs/design/`, when present, contains focused design documents.
- `docs/research/`, when present, contains external API, platform, and technical research.
- `docs/archive/`, when present, contains non-current documents and is historical context only.

Other project documents can live directly under `docs/`. Use lowercase kebab-case
filenames except for the fixed `PRD.md` and `ARCHITECTURE.md` names.
```

When the destination is confirmed, add this section with the actual repository:

```markdown
## Task tracking

Track approved delivery work in GitHub issues at https://github.com/<owner>/<repo>.
When asked to resume tracked work or select the next task, use
`planning-and-task-breakdown` to inspect this tracker and identify the next
implementation-ready task. Create tickets only when explicitly requested, using
`to-tickets`.
```

Keep existing tracking guidance unchanged when its replacement is unresolved.
Create directories only when an approved document needs them. Do not add empty
documents, placeholder directories, `.gitkeep` files, templates, configuration
files, or setup reports.

Apply only approved moves and renames. Update every clear repository-local link
and instruction pointer affected by those path changes. Apart from reference
repairs, preserve document contents exactly. Leave unresolved destination
conflicts untouched.

Setup approval authorizes these file changes only. Do not commit or push them.

## Finish

Setup is complete when exactly one canonical `## Project documentation` section
exists, the approved tracker and resume pointer are recorded in one canonical
`## Task tracking` section (or unresolved tracking is left unchanged), every
approved move and rename is applied, affected repository references resolve to the new paths, destination conflicts remain untouched, and unrelated
files and working changes remain unchanged.

Report only:

```text
AGENTS.md: created or updated
Task tracking: <GitHub owner/repo, or unresolved and left unchanged>
Organized: <moved or renamed documents, or none>
References repaired: <count or none>
Left in place: <ambiguous or conflicting documents, or none>
```

Stop after this report.
