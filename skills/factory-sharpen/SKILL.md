---
name: factory-sharpen
description: Stage 1 of the factory. Interrogate a loose idea one question at a time until it is a frozen contract, then write factory/SPEC.md — objective, non-goals, decisions, and acceptance criteria that define done. Use when starting a new build, or when requirements are vague. Triggers - "sharpen this", "new build", "factory sharpen", "turn this idea into a spec".
---

# Sharpen

Turn a loose idea into a contract an unattended loop can execute without
interpreting anything. Every ambiguity that survives this stage gets resolved by
an agent that cannot ask you a question — usually wrongly, usually at 2am.

Ground rules: `AGENTS.md`. Output: `factory/SPEC.md`. Then stop.

## Part 1 — Interrogate

- **One question at a time.** Wait for the answer before the next one. A wall of
  questions is bewildering and gets answered shallowly.
- **Attach your recommended answer to every question**, with one line of why. The
  human can accept in two words or push back.
- **Look up facts; ask only about decisions.** Stack, test command, existing
  structure, how it runs — read the codebase. Confirm what you found in one line
  rather than asking.
- **Order by blast radius.** The question whose answer changes the most goes
  first: who it's for → what done looks like → what's explicitly out → platform
  and data realities → the risky unknowns.
- **Cap: 8 questions.** Needing more means the idea isn't ready to build; say so
  and propose cutting scope.

Cover at minimum:

1. What are we building, in one sentence?
2. Who uses it, and what do they walk away with?
3. What does done look like — what would you *do* with it the morning it works?
4. What is explicitly **not** in this version?
5. New project or existing codebase? (If existing: read it now.)
6. What touches the outside world — payments, logins, email, third-party APIs?
   Each is a risk to record, not a reason to stop.
7. What's the riskiest or least-clear part, in your view?

If nothing meets the bar because the idea is already sharp, say so in one line
and write the spec. That is a successful run, not a failure.

## Part 2 — Write the criteria

Acceptance criteria are the whole point of this stage. Everything downstream
grades against them.

**The formula — behavior plus evidence:**

> **Given** \<state or input\>, **when** \<command or action runs\>, **then**
> \<something observable is true\>.

- Bad: "Login works." "Handles errors gracefully." "Fast enough."
- Good: "Given a wrong password, when the user submits the login form, then they
  see 'wrong email or password' and are not taken to the dashboard."
- Good: "Given a 5MB upload, when the import runs, then it completes in under 10
  seconds and the report lists every skipped row."

If a criterion doesn't name both the action and the observable, a verifier will
rubber-stamp it. Rewrite until it does.

**Tag every criterion with a tier:**

- `[t1]` — free and offline. Pure logic, parsers, validation, error paths, one
  end-to-end smoke run. **Runs every pass.**
- `[t2]` — costs money, time, or touches a live service. Gated behind an env var.
  **Runs at slice boundaries and once before sign-off.**

**Error paths are not optional.** An unattended loop lives in them. Every
feature area gets at least one criterion for its most likely failure, with the
exact expected behavior.

**If the build calls a model,** assert properties rather than exact output:
schema validity, values in legal ranges, required parts non-empty. For quality,
write a 2–4 axis rubric with a named good example and let a fresh agent grade
against it.

Aim for 10–25 criteria. Fewer gets rubber-stamped; more usually means the scope
is too big for one run.

## Output: `factory/SPEC.md`

```markdown
# Spec: <project>
created: <YYYY-MM-DD>
status: draft

## What we're building
<2-4 sentences, plain language>

## Who it's for and what they get

## Not in this version
<scope creep is what kills unattended runs>

## Facts (discovered, not asked)
<stack, test command, how it runs, existing structure — with file paths>

## Decisions
<one line per decision made during the interview — this is the record
the loop is not allowed to reinterpret>

## Acceptance criteria
- [t1] AC-1: Given ..., when ..., then ...
- [t1] AC-2: Given ..., when ..., then ...
- [t2] AC-3: Given ..., when ..., then ...

## Appended
<empty until factory-judge adds to it — see AGENTS.md>

## Risks
```

## Close out

1. Read the spec back in 3–4 sentences and get an explicit yes. **This is human
   gate 1.** On yes, flip `status: draft` → `status: approved`.
2. Say that `factory-plan` is next. **Do not plan. Do not write code.** Stop.

If you were re-run because the human amended a criterion (AGENTS.md rule 6),
confirm the dated note is under `## Decisions`, then take the spec through gate 1
again. Amending is the human's move; never make it on your own initiative.
