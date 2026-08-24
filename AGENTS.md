# Repository Purpose

This is charliesbot's personal skills repository. Its skills sustain creative momentum and continuity across side projects. They help the repository owner start new ideas, iterate on existing work, resume without reconstructing context, and ship.

Optimize for the repository owner's workflow. The repository may be public, but broad applicability is not a goal.

## Operating Principles

- **Creativity first.** Structure exists to keep ideas moving. When structure and momentum conflict, simplify the structure.
- **Consistency for recognition.** Familiar defaults should make every project quick to understand after time away.
- **Minimal by necessity.** Add only the detail that removes a recurring decision, preserves valuable context, or prevents a known failure. Minimal does not mean underspecified.
- **Stable defaults.** Prefer a dependable current workflow over constant optimization or support for every possible approach.
- **Durable reasoning.** Preserve why a non-obvious decision was made. Leave details that are easy to rediscover in the code or environment.

## Skill Design

Add or change a skill when recurring friction interrupts the creative loop. A useful skill does at least one of these:

- makes starting or resuming easier;
- makes the next action obvious;
- removes a repeated decision;
- preserves reasoning worth remembering; or
- shortens the path to usable or shipped work.

Treat each skill's frontmatter `description` as its routing contract. Front-load what the skill does and name each distinct situation that should trigger it, using one clear trigger per behavior branch rather than lists of synonyms.

Each skill may support a different part of the loop: exploring, capturing, building, resuming, or shipping. A skill does not need to cover the entire journey.

Build for the workflow that exists. Avoid configurable machinery for hypothetical users. If following a skill repeatedly feels like administrative work, simplify or remove it.

## Structure

Skills live at `skills/<name>/SKILL.md`. Add `references/`, `scripts/`, `assets/`, or `evals/` only when the skill needs them.

Keep the main skill focused on the actions and decisions needed every time. Place branch-specific detail behind clear pointers. Give each instruction one authoritative home rather than copying it between files.

## Prose

No em-dashes anywhere in this repo's prose (`SKILL.md` files, docs, `README.md`, `CHANGELOG.md`, ADRs, changesets, and code comments). Where a sentence reaches for one, rewrite it with a comma, colon, period, parentheses, or conjunction, whichever the sentence actually wants. Never do a blind character substitution.

## Artifacts and Handoffs

Skills may build on artifacts produced by other skills, but they do not form a required pipeline. Each artifact should have one job and remain useful even when no later skill runs.

Treat existing artifacts as sources of truth. Read the relevant ones, preserve their intent, and link to them instead of duplicating their contents. Do not turn every discussion into paperwork. Create a durable artifact when the user requests one or the invoked skill explicitly owns it.

## Evolving the System

Prefer stable conventions over novelty. Repeated friction justifies changing a default. One unusual project justifies an exception, not a new abstraction.

Keep the repository aligned with the owner's current workflow. Update or remove stale guidance instead of accumulating compatibility layers. The system is working when it leaves the owner closer to a meaningful result with less context to reconstruct and fewer decisions to repeat.
