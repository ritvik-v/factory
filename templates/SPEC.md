# Spec: <project>
created: <YYYY-MM-DD>
status: draft          # draft → approved (human gate 1)

## What we're building
<2-4 sentences, plain language>

## Who it's for and what they get

## Not in this version
<scope creep is what kills unattended runs>

## Facts (discovered, not asked)
<stack, test command, how it runs, existing structure — with file paths>

## Decisions
<one line per decision made during the interview.
This is the record the loop is not allowed to reinterpret.>

## Acceptance criteria
<[t1] = free and offline, runs every pass.
 [t2] = costs money or time, gated, runs at slice boundaries.
 Formula: Given <state>, when <action>, then <observable>.
 Include at least one error path per feature area.>

- [t1] AC-1: Given ..., when ..., then ...
- [t1] AC-2: Given ..., when ..., then ...
- [t2] AC-3: Given ..., when ..., then ...

## Appended
<Written only by factory-judge. Append-only: never edit or delete an
original criterion. Provenance required. These are permanent regression
tests and run every pass from then on.>

## Risks
