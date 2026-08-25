# Root `AGENTS.md` — the rail

The one document every agent loads. It holds what is true everywhere, the protocol for keeping the docs honest, and the index that makes everything else reachable.

Keep it short. It is loaded on every single request in this repo, whether or not it is needed. Detail goes in a child doc or a `context/` file; the rail points at them.

## Section order

```md
# <Project name>

<One or two sentences: what this project is, who it serves.>

## Documentation protocol

<The protocol block — copy from below, verbatim.>

## Project-wide rules

<Rules that hold in every directory. Numbered or bulleted, imperative.>

## Preferences

<Durable instructions the user has given. Empty until they give one.>

## Index

<The index — format below.>
```

Omit `Preferences` on bootstrap; add it the first time the user states a durable preference.

## The protocol block

Write this into the rail verbatim. It is addressed to whatever agent reads the repo next, which is why it is in second person.

```md
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
```

## The index

Two groups. Every entry needs a "read this when…" hook — without one, an agent cannot tell whether it needs the file, so it either reads everything or nothing.

The cross-cutting group may point directly to an authoritative existing `README.md`, PRD, specification, schema, or document under `docs/`. Index the source instead of copying it into `context/`. Include `context/README.md` only when that folder has three or more indexed entries besides the README itself.

Verify the relevant claims before calling an existing document authoritative. When it
conflicts with code/config or another current source, report the drift and do not route
agents to it for that subject. An index gives a document authority; link integrity alone
is not enough.

```md
## Index

### Cross-cutting

- [README.md](./README.md) — read for product scope and local setup
- [PRD.md](./PRD.md) — read before changing product behavior or scope
- [context/architecture.md](./context/architecture.md) — read before adding a module or
  changing how two modules talk
- [context/workflows.md](./context/workflows.md) — read before running builds, tests, or
  deploys
- [context/gotchas.md](./context/gotchas.md) — read before touching auth, migrations, or
  the payment path
- [context/glossary.md](./context/glossary.md) — read when naming anything user-facing
- [context/decisions/](./context/decisions/) — read when you want to change something that
  looks wrong; the reason may be in here

### By area

- [src/api/AGENTS.md](./src/api/AGENTS.md) — HTTP handlers, request validation, auth middleware
- [src/worker/AGENTS.md](./src/worker/AGENTS.md) — background jobs, queue consumers, retries
- [src/ui/AGENTS.md](./src/ui/AGENTS.md) — React components, routing, client state
```

Both directions must hold for agent-context docs: every nested `AGENTS.md` and every `context/` entry on disk appears here, and every entry here exists on disk. Existing project docs appear when they carry agent-relevant context; do not index every document indiscriminately. A broken index is worse than no index — it teaches the agent to stop trusting the rail.

Trackability is part of reachability. Check each indexed agent-context path against all
effective ignore rules, including nested `.gitignore` files. An ignored child rail may
work locally while leaving a broken index after commit or on a fresh clone; identify it
as local-only in the report and leave the ignore policy unchanged.

## Project-wide rules

Only rules that genuinely hold in every directory. The test: name a directory where the rule does not apply. If you can, it is not a project-wide rule — push it down.

```md
## Project-wide rules

1. TypeScript strict mode. No `any`, no `@ts-expect-error` without an adjacent comment
   explaining why.
2. Never edit files in `src/generated/` — regenerate with `pnpm codegen`.
3. All money values are integer minor units. Never floats. See `context/glossary.md`.
4. Database access goes through the repository layer, never raw SQL in handlers.
```

Concrete and checkable. "Write clean code" is not a rule; it is decoration.

## Agent-specific files

**Never create one.** `AGENTS.md` is the deliverable. Generating `CLAUDE.md`, `.cursorrules`, or `.github/copilot-instructions.md` on a user's behalf litters their repo with vendor files they did not ask for.

If one **already exists**, treat it as a source during Adopt: fold its real content into the rail, then leave it in place. Do not rewrite it and do not delete it — the user put it there.

One case needs reporting rather than action. Claude Code reads `CLAUDE.md`, not `AGENTS.md`, so on that client the rail is invisible without a bridge file. When the repo has no `CLAUDE.md`, say so in your report and give the user the line to add if they want it:

```md
@AGENTS.md
```

That `@` prefix is an import — Claude Code expands the file into context at session start. A markdown link like `See [AGENTS.md](./AGENTS.md)` does **not** work: it is loaded as literal text, not followed. If a repo already contains a `CLAUDE.md` using the link form, flag it as broken during a drift pass.

Whatever exists, keep one source of truth. Never maintain the same prose in two files — the copies drift apart within weeks.
