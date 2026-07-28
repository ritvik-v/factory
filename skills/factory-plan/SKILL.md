---
name: factory-plan
description: Stage 2 of the factory. Break an approved factory/SPEC.md into dependency-ordered vertical slices, each naming the acceptance criteria it satisfies, and write factory/PLAN.md. Use after factory-sharpen, before any code. Triggers - "plan the build", "slice this up", "factory plan".
---

# Plan

Cut the contract into slices an unattended loop can execute one at a time.
The plan says *what each slice must make true*, never *which keystrokes to
type* — prescribed steps encode one model generation's pace and rot within
months; a done-condition doesn't.

Requires `factory/SPEC.md` with `status: approved`. If criteria are missing or
the spec isn't approved, stop and run `factory-sharpen` first. Ground rules:
`AGENTS.md`. Output: `factory/PLAN.md`. Then stop.

## Slicing rules

- **Vertical, never horizontal.** Each slice cuts a narrow but complete path
  through every layer it touches — storage, logic, interface, tests. "Build the
  data layer" is a horizontal slice and is forbidden. "User can save an item and
  see it after a refresh" is a slice.
- **Slice 1 is always the walking skeleton** — the thinnest end-to-end path that
  actually runs. One page, one action, one saved thing. Everything after it keeps
  the app runnable.
- **A slice is one loop pass.** An agent should finish it, verify it, and commit
  it in a single focused context. Too big to hold at once → split.
- **Every slice names the criteria it serves** by ID. A criterion no slice claims
  is a planning gap — surface it. A slice claiming no criterion is scope creep —
  cut it.
- **Every slice ends runnable.** A loop that can always run the thing can always
  verify itself.
- **Wide mechanical changes are the exception.** A rename or retype whose blast
  radius crosses the whole codebase can't land green as a vertical slice.
  Sequence it expand → migrate in batches → contract, each its own slice.
- **6–20 slices.** Fewer means they're too big to verify. More means the scope is
  too big for one run — say so and propose cutting scope, not compressing slices.

## Order and risk

Dependency order, blockers named explicitly. Tag slices that touch external
services, auth, payments, or data migrations as `risk: high` so the loop
sequences them deliberately rather than discovering them at 3am.

Where the codebase makes a slice harder than it needs to be, put the prefactor
first as its own slice. Make the change easy, then make the easy change.

## Output: `factory/PLAN.md`

```markdown
# Plan: <project>
spec: factory/SPEC.md
created: <YYYY-MM-DD>
status: draft

## T1 — <observable behavior, walking skeleton>
- delivers: <what works end to end after this, from the user's side>
- satisfies: AC-1, AC-4
- blocked by: none
- risk: low

## T2 — <observable behavior>
- delivers: ...
- satisfies: AC-2
- blocked by: T1
- risk: high (calls the payments API)

## Coverage
<every AC in SPEC.md mapped to the slice that satisfies it;
name any AC no slice claims>
```

## Close out

1. Walk the human through it in one screen: the slices in order, what exists
   after each, which are risky and why, and any criterion nothing covers.
2. Get an explicit yes. **This is human gate 2 — the last one before the loop
   runs.** On yes, flip `status: draft` → `status: approved`.
3. Say that `factory-build` is next. **Do not start building.** Stop.
