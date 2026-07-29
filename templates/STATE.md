# State: <project>
spec: factory/SPEC.md
plan: factory/PLAN.md
branch: <feature branch>
caps: 25 passes · 8 hours
exit:                  # empty while running; then complete | blocked | stalled | limited | failed

<!-- This file is the run's only memory. Assume the session dies at any
     moment: a fresh agent must read this and resume without asking. -->

## Run summary
<written at exit: outcome first, then per-slice status, parked items with
reasons, and the exact commands a human can run to see it work>

## Slices
| id | status | attempts | verified criteria | notes |
|----|--------|----------|-------------------|-------|
| T1 | pending / in progress / verified / parked | 0 | | |

## Verdicts
<per criterion, per slice: the check that encodes it, then PASS / FAIL /
UNVERIFIED with the command run and what it printed. A criterion not run is
UNVERIFIED, never PASS; a criterion with no encoding cannot be run at all.

 - AC-1 · encoded by: tests/import.spec.ts "skips malformed rows"
   PASS — `npm test -- import` → 14 passing>


## Deviations
<one entry per departure from the plan:
 - plan said: ...
 - code forced: ...
 - chose: ...
 - lesson: ...            (cite file locations)
factory-judge reads this section first.>

## Parked
<slice id · what was tried · the question or blocker, written so a human
can answer it cold>

## Failed approaches
<what was tried and the evidence that killed it. No later pass re-walks
anything listed here.>

## Discovered scope
<work found mid-run that is outside the contract. Queued here, never
merged into the live goal.>

## Log
<one line per pass: [date time] <slice id> | what happened>
