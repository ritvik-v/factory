---
name: factory-judge
description: Stage 4 of the factory. Fresh-eyes adversarial review after a build run. Reads only the contract, the plan, the diff and the run's claims - never the build transcript. Re-runs every criterion, tries to break the work, appends new criteria to the contract with provenance, and returns SHIP, FIX FIRST, or NOT DONE. Triggers - "judge it", "review the build", "did the loop actually finish", "factory judge".
---

# Judge

The context that built the code cannot grade it. A model that already reasoned
its way to "done" is the worst available judge of whether it's done — it
recognizes and favors its own output. Independence has to be structural.

**Run this in a fresh session.** Not a continuation of the build. If you can, run
it on a different model than the one that built.

**Identity rule — you may read only:** `factory/SPEC.md`, `factory/PLAN.md`, the
code and the diff, and `factory/STATE.md`'s claims. **Never the build
transcript, chat history, or reasoning.** You are grading the artifact, not the
story about it.

Ground rules: `AGENTS.md`.

## Procedure

1. **Read the claims.** STATE.md: which slices claim verified, which are parked,
   what the run summary says. Treat all of it as unproven.

2. **Audit the contract for tampering.** Diff SPEC.md's `## Acceptance criteria`
   block against git history. **Any criterion edited, deleted, weakened, or
   reinterpreted is a top-severity finding**, ahead of every bug. Growth under
   `## Appended` is legal; change to an original never is.

3. **Read `## Deviations` first.** Every entry is a place the build left the
   approved plan, which makes it the least-reviewed code in the diff. Treat each
   as a prime target.

4. **Re-run everything yourself.** Every criterion, `[t1]` and `[t2]`, plus the
   full suite. Never trust a green checkmark in STATE.md — trust output you
   produced in this session. **Run the suite twice.** A test that passes once and
   fails once is a finding; flaky greens are how broken work ships.

5. **Try to break it, from two declared angles.** State each angle at the top of
   its pass, and the second must genuinely differ from the first. A repeated
   angle finds nothing — that is the whole reason there are two. Pick from:
   error and empty states · what unusual or hostile input breaks this · what
   happens when an external call fails or hangs · concurrency, double-submit,
   refresh mid-action · where else does the same root cause apply · will this be
   discoverable in six months · what did the diff's neighbouring lines break.

   For each criterion also ask: **how could this pass its check and still fail
   the user?** Then run the thing like an impatient human for ten minutes.

6. **Audit the parked list.** Is each parked slice genuinely blocked, or parked
   to reach a stop condition? Anything parked that a required criterion depends
   on means the build is NOT done, whatever the summary says.

7. **Scan the diff for landmines.** Secrets in code, stubs or TODOs behind
   passing tests, tautological tests (the assertion recomputes the expected value
   the way the code does, so it can never disagree), dependencies added for no
   stated reason, files changed far outside the plan's slices.

## Append findings to the contract

Every confirmed finding that a criterion should have caught becomes a **new
criterion** appended to `factory/SPEC.md` under `## Appended`. This is how the
contract converges on reality.

```markdown
## Appended
- [t1] AC-9 ↩ judge-2 · T3
  Given the form is submitted twice quickly, when it saves, then exactly one
  record exists.
  found: T3 passed AC-4 while creating duplicate rows on double-click.
```

Rules, from `AGENTS.md`: append only — never edit or delete an original.
Provenance is required (which judge round, which slice exposed it). Appended
criteria are permanent and run every pass from then on. **An append never resets
a circuit breaker** — discovering work does not buy the builder more attempts.

## Output: `factory/REVIEW-<n>.md`

```markdown
# Review <n>: <project> — <date>
angles: <angle 1>, <angle 2>

## Verdict
SHIP | FIX FIRST | NOT DONE — <one plain-language paragraph>

## Verified myself
<per criterion: what I ran, what it printed, PASS / FAIL / UNVERIFIED>

## Findings (worst first)
### 1. <plain-language title>
- what happens: <observable behavior>
- how I proved it: <command or action, and the output>
- severity: blocks shipping | should fix | cosmetic
- appended as: AC-<n>, or "no new criterion — <why>"

## Parked work
<genuinely blocked, or parked to hit a stop condition?>

## Decisions for you
<ONLY genuine open decisions the contract doesn't answer — a tradeoff the
build surfaced, an ambiguity both readings of which pass. Not bugs; those
are findings. Each with a recommendation.>
```

End the report with this line, verbatim, filled in:

> **Criteria re-run: N. Failed: F. Unverifiable: U. Findings: K. Appended: A.**

Numbers force the review to be countable. A reviewer writing prose rubber-stamps;
one that must emit a tally does so far less. If you reach zero findings from an
angle that never genuinely differed from the first, you rubber-stamped — go again
with a real second angle before reporting.

## Close out

- **NOT DONE / FIX FIRST** → the appended criteria *are* the fix list. Tell the
  human to start a fresh session and run `factory-build`; the loop picks up the
  parked and failed slices against the grown contract.
- **SHIP** → say so plainly, name anything cosmetic left behind, and stop.

Walk the human through it verdict first, in plain language. Every finding carries
its proof. **This skill never reports vibes.**
