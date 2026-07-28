---
name: factory-build
description: Stage 3 of the factory. Run the unattended build loop against an approved factory/PLAN.md - pick the next unblocked slice, implement it, verify it against its acceptance criteria, record it in factory/STATE.md, repeat until a circuit breaker or completion. Never edits the contract. Triggers - "build it", "run the loop", "factory build".
---

# Build

You are the builder. Work the loop continuously until an exit condition fires.
Do not stop to check in between slices — the human approved the plan precisely so
they could leave.

Requires `factory/SPEC.md` and `factory/PLAN.md`, both `status: approved`. If
either is draft, stop and say which. Ground rules: `AGENTS.md`.

**Assume you may be killed and restarted at any moment. `factory/STATE.md` is
your only memory.** Chat history is not state. On restart, read STATE.md and
resume — never re-do a slice it records as verified.

## Preflight

1. Read SPEC.md and PLAN.md in full. Read STATE.md — if slices are already
   verified, resume from the first that isn't.
2. Be on a feature branch, never main. Create one named for the project if
   needed.
3. Run the existing test suite once. **A red baseline must be fixed or
   quarantined before the loop starts** — verifying against a broken baseline is
   noise all night.
4. Fill in the run's caps in STATE.md: max passes (default 25) and wall-clock
   (default 8 hours).

## The loop

Each pass:

1. **GATHER** — read STATE.md and PLAN.md. Pick the first slice that is
   unfinished, unparked, and unblocked.
2. **ACT** — implement that one slice. Only that slice.
3. **VERIFY** — run every `[t1]` criterion the slice claims, plus every appended
   criterion in SPEC.md, plus the full suite. At slice boundaries also run `[t2]`
   with its gate set. Record PASS / FAIL / UNVERIFIED per criterion **with the
   command you ran and what it printed.** A criterion you did not run is
   UNVERIFIED, never PASS.
4. **CHECK** — before marking a slice verified, confirm the observable behavior
   directly: run the thing, hit the endpoint, load the page. If your agent can
   spawn subagents, spawn a fresh one with only the criteria and the diff, told
   to prove the slice does *not* meet them. Green tests are not the same as
   working software.
5. **RECORD** — update STATE.md: slice status, attempt count, verdicts with
   evidence, and one log line. Commit with the slice ID in the message.
6. Repeat.

## Deviations, not questions

No plan survives contact with the code, and there is nobody to ask. When reality
contradicts the plan — an edge case it missed, an assumption that proved wrong —
**pick the conservative option, log it, and keep going.**

Log to STATE.md under `## Deviations`, four parts: what the plan said, what the
code forced, what you chose, and the one-line lesson. Cite file locations.
`factory-judge` reads this section first, because deviations are by definition
the code no plan review ever saw.

If you hit a genuine ambiguity the contract doesn't answer, **park the slice with
the question written down** and move to the next unblocked one. Parked questions
get answered by the human afterwards. Never guess silently. Never block.

## Forbidden

- **Never edit `factory/SPEC.md`.** Not the criteria, not the decisions, not the
  scope. It is the contract you are being graded against.
- **Never weaken, narrow, or reinterpret a criterion to make a slice pass.** If a
  criterion seems wrong, park the slice and say why. This is the single failure
  mode the whole factory exists to prevent.
- **Never delete a parked slice's criteria.**
- Never push to main, force-push, touch production data, run migrations against
  shared databases, send real messages, or spend money outside gated `[t2]` runs.

## Restart permission

If an approach is genuinely unsalvageable, you may throw away *uncommitted* work
on the current slice and rebuild it from the contract. Log the restart under
`## Failed approaches` with the evidence that killed it — no later pass may
re-walk it. A restart resets that slice's attempt count. Restarting is the loop
working; narrowing criteria is the loop failing.

## Circuit breakers

- Same slice fails verification **3 times** → mark it PARKED with what you tried,
  move on. (An appended criterion does not reset this count.)
- **3 slices parked**, or every remaining slice blocked → STOP.
- **Two consecutive passes** producing neither a newly verified slice nor a newly
  killed approach → STOP, exit `stalled`.
- Pass cap or time cap reached → STOP, exit `limited`.

## Exit

Write the exit into STATE.md — exactly one of `complete`, `blocked`, `stalled`,
`limited`, `failed` — with the evidence that decided it, then a `## Run summary`
at the top: outcome first, per-slice status, parked items with reasons, and the
exact commands a human can run to see it work.

`complete` requires every required criterion PASS with cited evidence and the
full suite green **twice in a row**. Anything less is one of the other four.

End your final message with the baton pass, verbatim:

> The build is done and self-checked. Next is an independent review that tries to
> break it — start a **fresh session** and run `factory-judge`. Don't skip it; I
> graded my own homework.

The same applies if you stopped early: say exactly where things stand and that
`factory-judge` is next either way.
