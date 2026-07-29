# The factory: working agreement

Ground rules for any agent working in a project that uses the factory. Cursor,
Codex, Gemini CLI and most agents read `AGENTS.md` natively; Claude Code reads it
through the one-line import in `CLAUDE.md`. If your agent reads neither, paste
this file into the chat at the start of the session.

The factory is four commands and three files:

```
factory-sharpen  →  SPEC.md   (contract)     ─ human gate 1
factory-plan     →  PLAN.md   (slices)       ─ human gate 2
factory-build    →  STATE.md  (the run)      ─ unattended loop
factory-judge    →  verdict                  ─ back to build, or done
```

Each command is plain markdown. If your agent has no skill mechanism, paste the
skill body into the chat — nothing here depends on a specific harness.

## The three files

Everything the factory knows lives in `factory/` at the project root. Never in
chat history.

- **`factory/SPEC.md`** — the contract. What we're building, what's out, the
  decisions made, and the acceptance criteria that define done.
- **`factory/PLAN.md`** — the slices. Dependency-ordered tasks, each naming the
  criteria it serves.
- **`factory/STATE.md`** — the run. Task status, attempt counts, deviations,
  parked work, failed approaches, discovered scope.

**The crash test:** a brand-new session — a different agent, a different day —
must read these three files and continue without asking the human to re-explain
anything. If it can't, the files are wrong, not the session.

## The contract is append-only

1. `factory-judge` may **add** acceptance criteria. **No agent** may edit or
   delete one — not the builder, not the judge.
2. Appended criteria carry provenance — which judge round found it, which task's
   "passing" work exposed it — and live under `## Appended` in SPEC.md.
3. Appended criteria are permanent. They run on every pass from then on. They
   are regression tests.
4. **An append never resets a circuit breaker.** Discovering work does not buy
   more attempts.
5. Weakening, narrowing, or quietly reinterpreting a criterion is the one
   forbidden move. If a criterion seems wrong, PARK the task with a note saying
   why. Never edit it to make work pass.
6. **The human owns the contract and may amend it.** Editing or retiring a
   criterion takes a dated line under `## Decisions` naming the criterion and
   the reason, and drops SPEC.md back to `status: draft` — gate 1 re-opens and
   needs a fresh approval before the loop runs again. A change without that note
   is tampering. Agents never take this path; it exists so a wrong requirement
   isn't a dead end.

## Verdicts

Every criterion resolves to exactly one of:

- **PASS** — you ran the check and read output confirming it.
- **FAIL** — you ran the check and it did not confirm it.
- **UNVERIFIED** — you did not run it. An unrun check is never PASS.

A verdict without cited evidence — the command you ran and what it printed — is
UNVERIFIED. **If you have not run the check in this message, you cannot claim it
passes.**

## The flow guarantee

Two human gates, both before any code exists: approving SPEC.md, and approving
PLAN.md. After that, `factory-build` runs unattended until it exits.

Mid-run the agent never asks a blocking question. Genuine ambiguity gets parked
with the question written into STATE.md, and the run moves to the next unblocked
task. A question dialog that times out or answers itself is a HALT, never
consent.

## Circuit breakers

- One task fails verification **3 times** → PARK it, move to the next unblocked
  task.
- **3 tasks parked**, or every remaining task blocked → STOP.
- **Two consecutive passes** with neither a newly verified task nor a newly
  killed approach → STOP (`stalled`).
- A hard cap on total passes or wall-clock, whichever hits first → STOP.

A run exits exactly one of `complete`, `blocked`, `stalled`, `limited`,
`failed`. Always write which one, with the evidence that decided it.

## How the agent works here

- **Facts are yours, decisions are the human's.** If an answer is discoverable —
  in the codebase, a config file, a README — look it up instead of asking.
- **Evidence before claims.** Never report work done on reasoning. Run the check,
  read the output, then claim.
- **The builder never grades its own work.** `factory-judge` runs in a fresh
  context and never reads the build transcript.
- **Plain language.** The human may not read the code. Define any unavoidable
  technical term in the same sentence.
- **Guardrails.** Work on a feature branch, never main. Never force-push. Never
  touch production data, run migrations against shared databases, send messages,
  or spend money outside explicitly gated checks. Secrets stay in env files and
  are never printed or committed.
