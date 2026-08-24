---
name: context
description: Create, detect, and maintain a project's agent-facing documentation — a root AGENTS.md rail, nested per-directory AGENTS.md files, and a context/ folder for cross-cutting docs (architecture, glossary, gotchas, decisions). Use when the user runs /context, asks to set up, audit, or refresh project context or AGENTS.md, or reports that the docs are stale, wrong, or missing. Also use after landing a meaningful change, to update the docs that change affected.
---

# Context

Build and maintain the documentation an agent needs to work in this repo without guessing.

## The model

```
AGENTS.md                      the rail: project-wide rules, preferences, index
context/
  README.md                    index of this folder
  architecture.md              system shape, module boundaries, data flow
  conventions.md               the unwritten rules
  workflows.md                 verified build / test / deploy commands
  glossary.md                  domain terms
  gotchas.md                   traps, footguns, do-not-touch
  decisions/ADR-0001-slug.md   why things are the way they are
src/api/AGENTS.md              local rules for this boundary
src/ui/AGENTS.md               local rules for this boundary
```

One rule decides where every fact goes:

> **True of one directory → that directory's `AGENTS.md`. True of the whole project, or of no particular directory → `context/`.**

Never write the same fact in both. If a fact in `context/` turns out to only apply to one directory, move it down. If the same fact appears in three sibling `AGENTS.md` files, move it up.

Why the split: local rules belong next to the code so they get updated when that code changes. Cross-cutting facts have no directory to live in, and `AGENTS.md` files are the wrong place for history.

`AGENTS.md` is the entry point because every major agent reads it. `context/` files are **not** auto-loaded — they are reachable only because the rail indexes them. An unindexed file is an invisible file.

## Step 1 — Survey. Always.

Never write a doc from a skim. Establish, from the repo itself:

- **Existing docs.** `AGENTS.md`, `CLAUDE.md`, `README.md`, `CONTEXT.md`, `.cursorrules`, `.github/copilot-instructions.md`, `docs/`, and any nested `AGENTS.md`.
- **Project type and manifest.** `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `composer.json`, etc. Pull the real script names from it.
- **Real boundaries.** Which top-level directories are actual modules with their own purpose, versus incidental folders.
- **Commands that work.** Read them out of the manifest or CI config. Do not invent a `npm test` that isn't there.
- **Enough code to be correct.** Read the entry points and the largest modules. Depth beats coverage.

Then pick a route:

| Repo state | Route |
| --- | --- |
| No agent docs at all | **Bootstrap** |
| `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, or `docs/` exist with real content | **Adopt** |
| Rail + `context/` already in place | **Update** |

State which route you took and why before writing anything.

## Bootstrap

1. **Write the rail first** — `AGENTS.md` at the root, per `references/AGENTS-ROOT.md`. It carries the protocol block, project-wide rules, and the index. Nothing else works without it.
2. **Add only the `context/` files you can fill.** Start with `architecture.md`, `workflows.md`, and `gotchas.md` — these have the highest value per token and are usually verifiable from the code. Add `glossary.md` when the domain has real vocabulary, `conventions.md` when the codebase has consistent unwritten style. Skip a file rather than pad it. Formats in `references/CONTEXT-FILES.md`.
3. **Create child `AGENTS.md` only at lasting boundaries** — a directory with its own purpose, its own rules, or its own contract with the rest of the system. Format in `references/AGENTS-CHILD.md`. Three well-placed child docs beat fifteen that restate the rail.
4. **Index everything.** Every `context/` file and every child `AGENTS.md` gets a line in the rail's index with a short "read this when…" hook. Unindexed means invisible.
5. **Point Claude at the rail.** If `CLAUDE.md` is absent, create it containing only a pointer to `AGENTS.md`. If it exists with real content, fold that content into the rail and replace it with the pointer. Never maintain the same prose twice.

No ADRs on bootstrap. You don't know why past decisions were made — say so with a `TODO(human)` marker instead of guessing.

## Adopt

Existing docs are evidence, and someone wrote them by hand. Preserve the human's words.

1. **Read what's there in full.** Treat it as the current contract.
2. **Sort each existing claim** by the placement rule: project-wide stays in the rail, directory-specific moves down to a child doc, cross-cutting detail moves into a `context/` file.
3. **Verify while you move.** Run the drift checks from `references/UPDATE.md` on every claim. A stale claim gets corrected or dropped, not relocated intact.
4. **Add the protocol block and the index** to the existing `AGENTS.md`. Keep their headings, their tone, their ordering. You are adding structure around their content, not replacing it.
5. **Fill gaps only.** If they already documented the architecture well, do not rewrite it into your own template.

Never delete a human-written statement because it doesn't fit the template. If it's true, find it a home. If you think it's false, say so in your report and leave it — do not silently drop it.

## Update

The drift pass. Full check procedure in `references/UPDATE.md`. In short:

1. **Verify every checkable claim.** Referenced paths still exist. Named commands still exist in the manifest. Indexed docs still exist. Documented directories still exist.
2. **Correct what drifted.** Amend in place. Do not regenerate a file to fix one line.
3. **Find what's undocumented.** New directories that became boundaries. New footguns. Removed modules whose child doc should now be deleted.
4. **Refresh the indexes** so they match the files on disk in both directions.
5. **Resolve contradictions** by keeping the more specific doc and deleting the duplicated claim from the broader one.

When invoked after a code change rather than as a standalone audit, scope the pass to the docs governing the paths you touched — walk from the rail down to those paths and update the nearest owning doc. Skip the pass entirely for cosmetic changes, and say that you skipped it.

## Hard rules

- **Write only what you verified.** Everything else is a `> TODO(human): <specific question>` marker. Docs that are confidently wrong are worse than absent, because the next agent trusts them.
- **An empty section beats an invented one.** Leave the heading, leave it blank.
- **Never overwrite a doc wholesale.** Amend the lines that are wrong. Hand-written "why" is the most valuable content in any of these files and regeneration destroys it.
- **Stable contracts, not diary entries.** Docs describe how things are now. Delete superseded statements instead of narrating the change. `context/decisions/` is the one exception — that is where history is allowed to live.
- **Broad rules up, specific details down.** A rule that holds everywhere belongs in the rail. On conflict, the doc closest to the code wins on local detail; the rail wins on project-wide policy.
- **Keep it operational.** Prose an agent can act on. No filler, no restating the obvious, no summarizing the README.
- **Don't document generated or vendored trees.** `node_modules/`, `dist/`, `build/`, `target/`, `.venv/`, lockfiles.

## Report

Close by stating: the route you took, the files created, the files amended and what changed in each, the `TODO(human)` markers you left and where, and any doc you deliberately did not touch and why.

## References

Read the one you need, when you need it.

- `references/AGENTS-ROOT.md` — root rail format, protocol block, index format
- `references/AGENTS-CHILD.md` — nested `AGENTS.md` format and when a directory earns one
- `references/CONTEXT-FILES.md` — format for each `context/` file and for ADRs
- `references/UPDATE.md` — the drift check procedure
