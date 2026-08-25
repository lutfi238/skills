# skills

A repository of agent skills, distributed through the `skills` CLI as
`lutfi238/skills`. Every file here is markdown read by an agent at runtime — there is no build step and no code.

## Documentation protocol

Documentation in this repo is hierarchical and binding.

**Before editing.** List the files you intend to change. Walk from this file down
to each of them, reading every `AGENTS.md` on the way and following the indexes
into the ones that cover your target. Read the `context/` files the index marks as
relevant. Re-read this chain each session — do not rely on remembered context.

**Precedence.** The nearest doc governs local detail. This file governs project-wide
policy. Where they conflict on a local matter the nearer doc wins; where they conflict
on policy this file wins. No child doc may weaken this protocol.

**After editing.** Update the nearest doc that owns what you changed, then propagate:
up if a project-wide rule changed, down if a boundary moved. Update when purpose,
structure, contracts, commands, dependencies, constraints, or boundaries change.
Cosmetic changes need no doc edit — but state that you checked and skipped.

**Where facts go.** A fact true of one directory belongs in that directory's
`AGENTS.md`. A fact true of the whole project, or of no particular directory, belongs
in `context/`. Never both.

**Style.** Stable contracts, not diary entries. Describe how things are now; delete
superseded statements rather than narrating the change. `context/decisions/` is the
only place history belongs. Leave a section empty rather than filling it with guesses.

**Closeout.** Re-check the docs covering every path you changed, refresh any index you
invalidated, delete contradictions, and report which docs you updated and which you
deliberately left alone.

## Project-wide rules

1. **A skill is `skills/<name>/SKILL.md`.** The `skills` CLI discovers any directory
   containing a `SKILL.md`, walking three levels deep from `skills/`. Keep the layout
   flat — a shallower `SKILL.md` shadows anything nested beneath it.
2. **Frontmatter is `name` and `description` only.** `name` must be lowercase with
   hyphens and match the directory name. Both fields are required by the CLI.
3. **No agent-specific frontmatter.** `context: fork` is Claude Code only.
   `allowed-tools` names tools that do not exist on every client. This repo targets all
   of them, so neither is used.
4. **No runtime dependencies.** No scripts, no npm packages, nothing to install. The
   skills must work identically on every client the CLI supports.
5. **`description` is the trigger.** It is the only part an agent reads before deciding
   whether to load the skill. Name the concrete situations that should invoke it, in
   third person. A vague description means the skill never fires.
6. **Keep `SKILL.md` short; push detail into `references/`.** `SKILL.md` is loaded in
   full when the skill fires. Reference files are read only when the procedure says to.
7. **Write instructions, not prose.** Imperative, operational, addressed to the agent
   that will act on them. No preamble, no summarizing what the section is about to say.
8. **Every rule states a consequence.** What breaks, and where. A rule an agent cannot
   check is decoration.

## Index

### By area

- [skills/context/SKILL.md](./skills/context/SKILL.md) — the `/context` skill: bootstrap,
  adopt, and drift-check a repository's agent-context graph while reusing authoritative
  project docs. Its `references/` hold the formats it emits.

No child `AGENTS.md` files and no `context/` folder yet — this repo is one skill and
eight rules, so neither has earned its place. Per
[references/AGENTS-CHILD.md](./skills/context/references/AGENTS-CHILD.md), a directory
gets its own doc when it is a lasting boundary with rules of its own. Add the second
skill and `skills/context/` will qualify.
