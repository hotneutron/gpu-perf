# Justifications for AGENTS.md rules

The five active entries below match the current AGENTS.md. Each records why the
rule exists and its introduction or observed revision date. Inherited dates are
preserved as historical provenance.

This file also serves as AGENTS.md's revision history. Rules removed from
AGENTS.md are retained separately with their original rationale and requests.
Removing an entry from the instruction file does not rewrite historical plans or
supersede the project-specific contracts that govern research.

## Rule: Amendments to an existing artifact must add/update a timestamped `## Revision History`; agent-facing artifacts are exempted.

- Added: 2026-07-29 (original artifact-history requirement).
- Updated: 2026-11-11 23:37:26 -0800 (current wording and agent-facing exemption recorded).
- Why: Timestamped history makes changes to plans, reports and other maintained
  artifacts auditable. Agent-facing instruction and guidance files are exempt so
  routine maintenance does not accumulate administrative text in their working
  instructions. This rationale file still records rule changes because it also
  serves as the revision history for AGENTS.md.
- Historical context: the inherited rationale recorded an agent-facing exemption
  dated 2026-08-01. The Nov 5 version of AGENTS.md omitted it; the current cleanup
  explicitly includes it. Prior revision entries below retain that earlier state.

## Rule: No `Co-Authored-By` trailer in commits.

- Added: 2026-07-29
- Why: Commit authorship should reflect the human author only; automated
  co-author trailers add noise and misattribute ownership in this repo's history.

## Rule: Every rule here must have a justification entry in `.agent/rules-rationale.md` (which doubles as this file's revision history).

- Added: 2026-08-01
- Why: Rules without recorded rationale become cargo-culted or silently dropped.
  Requiring a justification keeps `AGENTS.md` terse (rules only) while preserving
  the reasoning, and gives `AGENTS.md` an audit trail without violating its own
  revision-history rule.

## Rule: Persist user prompts verbatim in artifacts.

- Added: 2026-08-03
- Why: Verbatim prompts make artifact intent and provenance auditable, easier to
  understand, and easier to debug. They also encourage concrete, thoughtful
  prompting and support sharing the user's thought process.

## Rule: Do not number headings or subheadings.

- Recorded: 2026-11-11 23:37:26 -0800 (introduced in this repository's current AGENTS.md cleanup).
- Why: Descriptive, unnumbered headings remain readable when sections are inserted
  or reordered and avoid manual renumbering. Use lists or tables when the content
  itself needs an ordered sequence; the rule applies to heading titles.
