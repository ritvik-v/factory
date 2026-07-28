# Plan: <project>
spec: factory/SPEC.md
created: <YYYY-MM-DD>
status: draft          # draft → approved (human gate 2)

## T1 — <observable behavior; always the walking skeleton>
- delivers: <what works end to end after this, from the user's side>
- satisfies: AC-1, AC-4
- blocked by: none
- risk: low

## T2 — <observable behavior>
- delivers: ...
- satisfies: AC-2
- blocked by: T1
- risk: high (<why — external service, auth, payments, migration>)

## Coverage
<every AC in SPEC.md mapped to the slice that satisfies it.
Name any AC no slice claims — that is a planning gap, not slack.>
