# AI-assisted engineering: explicit decisions, bounded changes, attributable evidence

[Back to showcase](../README.md) · [Workflow diagram](../diagrams/ENGINEERING_WORKFLOW.md)

AI collaboration is part of this project's development process. Its role includes technical analysis, implementation assistance, review, verification support, and documentation. The Owner retains material product decisions and repository/release governance. Production coordination and acceptance are distinct from implementation.

This page is a public methodology summary. Raw project instructions, agent transcripts, private decision records, and internal production workflows are not reproduced.

## Separate feasibility from product readiness

Before implementation, the process asks two separate questions: can the architecture safely support the work, and is the player-facing behavior sufficiently decided?

If behavior is ambiguous, code stops at that boundary. The process compares a small number of meaningful options, records the decision in its durable authority, and then resumes implementation. Technical feasibility cannot substitute for an unresolved product choice.

## Use the right authority for each question

| Question | Evidence or authority used |
|---|---|
| What does the implementation actually do? | Exact source at the relevant state |
| What was tested? | Attributable execution evidence |
| What is accepted? | Accepted checkpoint and production acceptance record |
| What behavior is intended? | Accepted contract or design decision |
| What work is authorized? | Current bounded task |
| What may be published or released? | Owner decision and applicable rights review |

An assistant's memory or confident explanation is not an additional authority. Conflicting facts require reconciliation; a convenient interpretation does not resolve them.

## Bound the change

The process establishes the baseline, affected boundary, intended behavior, exclusions, and acceptance obligations before mutation. It requires current-file inspection rather than rebuilding code from remembered snippets. New abstractions must answer current needs rather than imagined future scope.

This also limits AI-generated drift: a local fix should not quietly introduce a new rules engine, alter unrelated behavior, or enlarge the ticket because a refactor looks attractive.

## Distinguish implementation, verification, and acceptance

Generated code is a candidate implementation. Verification establishes what happened against an identified state. Acceptance is a separate decision using those results and explicit limits. A human risk decision cannot turn failed or missing evidence into a passing test.

Evidence is labeled as current verified, inherited accepted, reported, or unknown. This showcase itself illustrates the distinction: it uses inherited documentation and reports no new game execution.

## Review the state being handed off

The current workflow amendment makes the pushed feature tip the remote handoff. Review checks the actual pull-request diff for intended files, unexpected deletions or renames, secrets, Unity asset identity coverage, and branch/base correctness. Exact-state acceptance precedes integration; a changed or conflicting state requires reconciliation.

This describes the process policy, not a new certification that every historical ticket followed the latest amendment.

## What this demonstrates

The engineering practice is accountable AI use: explicit ownership, current-source inspection, bounded tasks, independent review where required, meaningful tests, and truthful evidence. It does not supply a measured AI productivity gain, an autonomous-development claim, or proof of who authored every line.

Future interview materials should identify the Owner's specific design, implementation, review, and tool-operation contributions from actual records. Clear attribution makes the systems work easier to assess.
