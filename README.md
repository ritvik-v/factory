# factory

A software factory with the ceremony removed. Four commands, three files, ~530
lines of plain markdown end to end. Works with any coding agent that can read
files, run commands, and follow a multi-step instruction.

```
factory-sharpen  →  SPEC.md   the contract        ─ human gate 1
factory-plan     →  PLAN.md   the slices          ─ human gate 2
factory-build    →  STATE.md  the unattended loop
factory-judge    →  verdict   fresh eyes, adversarial
                              ↳ appends criteria to the contract, loops back
```

You approve the contract and the slicing. After that the loop runs on its own
until it finishes or a circuit breaker stops it. Then a fresh, uncontaminated
session tries to break the result, and every real finding becomes a **new
permanent criterion** on the contract — so the spec converges on reality instead
of drifting away from it.

## Install

Any agent, one command — [skills.sh](https://skills.sh) installs into 70+ agents
(Claude Code, Cursor, Codex, Copilot, Cline, Gemini CLI, and more):

```bash
npx skills@latest add ritvik-v/factory
```

Or copy `skills/*` into your agent's skills directory by hand
(`~/.claude/skills/`, `~/.cursor/skills/`, `~/.codex/skills/`). Full per-agent
wiring — including agents with no skill mechanism at all — is in
[INSTALL.md](./INSTALL.md).

Then, in any project:

```
factory-sharpen   # or /factory-sharpen, depending on your agent
```

## The thesis

A software factory is not a pipeline. It's **one loop plus two gates, wrapped
around a contract that neither the builder nor the reviewer may edit.** Five
claims hold it up:

**1. The verifier is the product; the code is a byproduct.** An agent produces
plausible artifacts far faster than anything can check them, so the binding
resource is trustworthy verdicts, not output. Acceptance criteria get written
*before* code and *frozen* — a criterion written afterwards is written by
something that already knows what the code does.

**2. Alignment is extracted by interrogation, not by prompting harder.** One
question at a time, your recommended answer attached to each, facts looked up
rather than asked, ordered by how much of the plan the answer would change.

**3. Files are the only memory.** Every context starts empty and dies without
heirs. A convention that lives only in a prior session is already lost. The
crash test: a brand-new session — different agent, different day — reads the
three files and continues without asking you to re-explain anything.

**4. Independence is structural, not requested.** "Review your own work
critically" does nothing; a model that already reasoned its way to *done*
recognizes and favors its own output. So `factory-judge` runs in a fresh session,
sees only the contract and the diff — **never the build transcript** — and is
told to prove the work fails. It reviews from two explicitly declared angles,
because a repeated angle finds nothing.

**5. Loops need a bound and a closed set of exits.** Never "loop until done."
Three failures parks a slice, three parked slices stops the run, and two passes
with neither a newly verified slice nor a newly killed approach exits `stalled`.
Every run ends as exactly one of `complete`, `blocked`, `stalled`, `limited`,
`failed`.

## The append-only contract

The one rule worth understanding before you use this:

- `factory-judge` may **add** criteria. Nothing may **edit or delete** one.
- Appended criteria carry provenance — which review round found it, which slice's
  "passing" work exposed it.
- They're permanent. They run every pass from then on. They are regression tests.
- **An append never resets a circuit breaker.** Discovering work doesn't buy the
  builder more attempts.
- Weakening a criterion to make work pass is the one forbidden move. A build that
  can't meet a criterion **parks the slice** and says why.

The useful side effect: **where appended criteria cluster tells you which stage
failed.** Several appends in one area means `factory-sharpen` under-specified
that area — not that the builder was sloppy. Over a few projects that's real
signal about your own intake questions.

## What this deliberately doesn't have

No stage proliferation, no five-round review chains, no gate matrices or
per-phase model assignment, no hash-pinned contract schemas, no forty-item
definition-of-done, no step-level plans that spell out "now run the test." Those
exist to make a process survive distribution across many people and many
projects. This one is built for one person, so it doesn't pay that tax. A
200-line contract is followed worse than a 25-line one even when the extra lines
are all true.

It also doesn't write your tests for you, replace reading the diff, or make an
unattended run safe to point at production.

## Prior art

Built by reading seven approaches and keeping only what was load-bearing:

- [MikeFishbeinAtherial/infinite-headcount](https://github.com/MikeFishbeinAtherial/infinite-headcount)
  — the closest ancestor: staged factory, tests before code, fresh-eyes review,
  circuit breakers.
- [simoncorry/foundry](https://github.com/simoncorry/foundry) — declared review
  angles (its bug-catch curve ran 4, 0, 4, 3, 1 — the round that repeated an
  angle found zero), the deviation log, the flow guarantee.
- [ryanthedev/code-foundations](https://github.com/ryanthedev/code-foundations)
  — gate failure protocol, retry caps, adaptive questioning.
- [obra/superpowers](https://github.com/obra/superpowers) — the ledger that
  survives compaction, the evidence-before-claims iron law, structural reviewer
  independence.
- [mattpocock/skills](https://github.com/mattpocock/skills) — the proof that
  grilling fits in twelve lines, vertical tracer-bullet slicing, tautological-test
  detection.
- [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) —
  assumption surfacing, the standing-bar-vs-per-task-criteria distinction.
- [DanMcInerney/orchflows](https://github.com/DanMcInerney/orchflows) — the
  verdict grammar (PASS/FAIL/UNVERIFIED), closed terminal states, the stall rule,
  and the token-economy argument for keeping all of this short.

## License

MIT
