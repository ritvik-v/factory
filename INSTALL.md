# Install

The factory is four `SKILL.md` files and one `AGENTS.md`. Nothing runs, nothing
compiles, there are no dependencies. Any agent that can read files, run shell
commands, and follow a multi-step instruction can use it.

## Any agent (recommended)

The open [skills CLI](https://skills.sh) installs into 70+ agents and handles the
per-agent directory layout for you:

```bash
npx skills@latest add ritvik-v/factory
```

## By hand

Copy the four skill folders into your agent's skills directory:

| Agent | Personal (all projects) | Project-scoped |
|---|---|---|
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| Cursor (2.4+) | `~/.cursor/skills/` | `.cursor/skills/` |
| Codex | `~/.codex/skills/` | — |
| OpenCode | `~/.config/opencode/skills/` | `.opencode/skills/` |

```bash
git clone https://github.com/ritvik-v/factory.git
cp -r factory/skills/* ~/.claude/skills/     # swap the target per the table
```

Installing into more than one directory makes the factory available in every
project in every tool. Restart your agent afterwards so it picks them up.

## The ground rules file

`AGENTS.md` holds the law the four skills share — the append-only contract, the
verdict grammar, the circuit breakers, the guardrails. Copy it to the root of any
project you run the factory in:

```bash
cp factory/AGENTS.md /path/to/your-project/AGENTS.md
```

Cursor, Codex, Gemini CLI and most agents read `AGENTS.md` natively. **Claude
Code** needs a one-line bridge — create `CLAUDE.md` beside it containing:

```
@AGENTS.md
```

## Agents with no skill mechanism

Everything here is plain markdown with no harness-specific syntax. Paste the body
of the skill you want into the chat and it runs. Paste `AGENTS.md` first so the
shared rules are in context.

## Templates

`templates/` holds the three file skeletons — `SPEC.md`, `PLAN.md`, `STATE.md`.
The skills write these themselves, so you don't need to copy them; they're there
to read when you want to see the shape, or to hand-start a run.

## Verify it took

Ask your agent: **"what factory skills do you have?"** It should name
`factory-sharpen`, `factory-plan`, `factory-build`, `factory-judge`. Then, in a
project:

```
factory-sharpen
```

## Renaming

The `factory-` prefix exists to avoid colliding with the `/plan`, `/build`, and
`/review` commands that ship with other skill sets. If you don't have those,
rename the folders and the `name:` field in each frontmatter to plain `sharpen`,
`plan`, `build`, `judge` — the cross-references between skills use the full names,
so update those too (`grep -rn 'factory-' skills/`).
