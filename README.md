# skills

Agent skills, installable into any agent the `skills` CLI supports — Claude Code, Cursor, Codex, Copilot, Windsurf, Gemini, Zed, Cline, OpenCode, and the rest.

```bash
npx skills add lutfi238/skills
```

## Skills

### `/context`

Creates, adopts, and maintains a repository's agent context as a documentation graph.

```
/context
```

It surveys the repo first, then picks a route:

| Repo state | What it does |
| --- | --- |
| No agent docs | **Bootstraps** the rail, reuses existing project docs, adds sparse `context/`, and creates child docs at real boundaries |
| Existing `AGENTS.md` / `CLAUDE.md` / `.cursorrules` | **Adopts** them — adds structure around your words, never overwrites |
| Docs already in place | **Drift pass** — checks every claim against the repo and fixes what rotted |

## The model

```
AGENTS.md                      the rail: project-wide rules, protocol, index
context/
  README.md                    optional index; only with 3+ other entries
  architecture.md              system shape, boundaries, data flow
  workflows.md                 verified build / test / deploy commands
  gotchas.md                   traps, footguns, do-not-touch
  conventions.md               the unwritten rules
  glossary.md                  domain terms
  decisions/ADR-0001-*.md      why things are the way they are
src/api/AGENTS.md              local rules for this boundary
src/worker/AGENTS.md           local rules for this boundary
```

This is not a fixed file scaffold. `/context` indexes an authoritative existing
`README.md`, PRD, specification, schema, or other project doc instead of copying it.
It creates a `context/` file only for verified cross-cutting knowledge with no better
home, so a healthy folder may contain only one file. The coverage report explains what
was reused, localized, created, and deliberately skipped.

One rule decides where every fact goes:

> **True of one directory → that directory's `AGENTS.md`. True of the whole project, or of no particular directory → `context/`.**

Two axes, deliberately. Location-local docs sit next to the code they govern, so they get updated when that code changes. Cross-cutting facts — the architecture, the glossary, the reason a decision was made — have no directory to live in, so they get a folder of their own.

`AGENTS.md` is the entry point because every major agent loads it automatically. Nothing loads `context/` or existing project docs on its own — the rail's index is what makes the graph reachable.

## Why not just write the docs yourself

You can, and you should keep writing the parts only you know. What this skill adds is the half nobody does by hand:

- **Drift detection.** Docs rot silently — nothing fails when a path reference goes stale, the agent just acts on a false claim. `/context` treats each doc as a set of assertions and checks them: do the referenced paths exist, do the named commands still exist in the manifest, does every indexed doc resolve, has a directory become a boundary without a doc.
- **Amend, never regenerate.** It fixes the lines that are wrong and leaves the rest byte-identical. Your hand-written reasoning is the most valuable content in these files, and regeneration destroys it.
- **It refuses to guess.** Anything it can't verify from the code becomes a `> TODO(human): <specific question>` marker instead of confident-sounding fiction. Docs that are wrong are worse than docs that are missing, because the next agent trusts them.
- **A hard write boundary.** It snapshots the existing worktree, declares its targets, and checks the final write set. Only `AGENTS.md` files and root `context/` are eligible; drift found in README, source, config, ignore files, or vendor docs is reported rather than edited.
- **Reachability beyond links.** It checks nested ignore rules and authority conflicts, so an indexed file must be present in the intended checkout and reliable for the subject its pointer advertises.

## Credit

The hierarchical-`AGENTS.md` model — local docs that govern their subtree, a read-before-editing walk down the chain, an update pass after every meaningful change — comes from [**DOX**](https://github.com/agent0ai/dox) by [Agent Zero](https://agent-zero.ai). If you want that convention on its own with no tooling, use DOX directly; it's one markdown file and it's excellent.

This repo reimplements the protocol in its own words and extends it in three ways: a `context/` folder for facts that belong to no single directory, ADRs for the decision history DOX's no-diary rule excludes, and verification of existing claims rather than only refreshing them. Any awkwardness in the result is ours, not theirs.

## License

MIT
