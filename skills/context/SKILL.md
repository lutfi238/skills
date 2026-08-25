---
name: context
description: Create, adopt, and maintain a repository's agent context as a documentation graph — a root AGENTS.md rail, existing project docs, nested per-directory AGENTS.md files, and sparse cross-cutting context/. Use when the user runs /context, asks to set up, audit, or refresh repository context or AGENTS.md, reports stale or missing agent docs, or lands a meaningful change whose governing docs need updating.
---

# Context

Build and maintain the documentation an agent needs to work in this repo without guessing.

## The model

```
AGENTS.md                      the rail: project-wide rules, preferences, index
context/
  README.md                    optional index; only with 3+ other entries
  architecture.md              system shape, module boundaries, data flow
  conventions.md               the unwritten rules
  workflows.md                 verified build / test / deploy commands
  glossary.md                  domain terms
  gotchas.md                   traps, footguns, do-not-touch
  decisions/ADR-0001-slug.md   why things are the way they are
src/api/AGENTS.md              local rules for this boundary
src/ui/AGENTS.md               local rules for this boundary
```

This is a documentation graph, not a fixed `context/` scaffold. Reuse and index an
authoritative `README.md`, PRD, specification, schema, or existing docs instead of
copying them. A healthy `context/` may contain one file: sparsity means the other facts
already have better homes, not that the run was incomplete.

One rule decides where every fact goes:

> **True of one directory → that directory's `AGENTS.md`. True of the whole project, or of no particular directory → `context/`.**

Never write the same fact in both. If a fact in `context/` turns out to only apply to one directory, move it down. If the same fact appears in three sibling `AGENTS.md` files, move it up.

Why the split: local rules belong next to the code so they get updated when that code changes. Cross-cutting facts have no directory to live in, and `AGENTS.md` files are the wrong place for history.

`AGENTS.md` is the entry point because every major agent reads it. `context/` files are **not** auto-loaded — they are reachable only because the rail indexes them. An unindexed file is an invisible file.

## Step 1 — Survey. Always.

Never write a doc from a skim. Establish, from the repo itself:

- **Existing docs.** `AGENTS.md`, `CLAUDE.md`, `README.md`, `CONTEXT.md`, `.cursorrules`, `.github/copilot-instructions.md`, `docs/`, any nested `AGENTS.md`, and authoritative product or requirements docs such as `PRD.md`, `REQUIREMENTS.md`, and `SPEC.md`.
- **Whether agent docs are gitignored.** Check `.gitignore` for `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, and similar. A repo that ignores them has decided agent docs stay local and uncommitted. Honour that: write the docs, leave `.gitignore` alone, and say in your report that they are untracked by project policy.
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
2. **Reuse before creating.** Index authoritative existing docs for the subjects they already cover. Add only the `context/` files you can fill with verified, nonduplicated facts. Start with `architecture.md`, `workflows.md`, and `gotchas.md` when those subjects lack a home. Add `glossary.md` when the domain has real vocabulary and `conventions.md` when the codebase has consistent unwritten style. Add product context only from an authoritative document or the user, never by inferring intent from implementation. Skip a file rather than pad it. Formats in `references/CONTEXT-FILES.md`.
3. **Create child `AGENTS.md` only at lasting boundaries** — a directory with its own purpose, its own rules, or its own contract with the rest of the system. Format in `references/AGENTS-CHILD.md`. Three well-placed child docs beat fifteen that restate the rail.
4. **Index everything.** Every `context/` file and every child `AGENTS.md` gets a line in the rail's index with a short "read this when…" hook. Unindexed means invisible.
5. **Never create agent-specific instruction files.** Not `CLAUDE.md`, not `.cursorrules`, not `.github/copilot-instructions.md`. `AGENTS.md` is the deliverable. Some clients need a bridge file to see it — say so in your report and give the user the exact line, but let them decide. See `references/AGENTS-ROOT.md`.

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
6. **Prune redundant context indexes.** Create `context/README.md` at three indexed entries besides the README itself; remove it when fewer remain. The rail is the index below that threshold.

When invoked after a code change rather than as a standalone audit, scope the pass to the docs governing the paths you touched — walk from the rail down to those paths and update the nearest owning doc. Skip the pass entirely for cosmetic changes, and say that you skipped it.

## Hard rules

- **Touch only `AGENTS.md` files and `context/`.** Nothing else. Not `.gitignore`, not `README.md`, not source, not config — even when you spot something wrong in them. Report it instead. A documentation pass that edits unrelated files is indistinguishable from an agent going off-task, and it is the fastest way to lose a user's trust in the tool.
- **Never delete or rewrite an existing agent file on your own initiative.** `CLAUDE.md`, `.cursorrules`, `GEMINI.md`, `.windsurfrules`, a hand-written `AGENTS.md` — read them, fold their content into the rail, and leave the originals exactly as they are. The user put them there. If the user asks you to remove one, do it — this rule constrains what you do unasked.
- **Never create an agent-specific instruction file.** `AGENTS.md` and `context/` are the deliverable. Report the bridge line for other clients; let the user add it.
- **Write only what you verified from a repository source of truth or the user.** Everything else is a `> TODO(human): <specific question>` marker. Implementation proves current behavior, not product intent or historical reasoning. Docs that are confidently wrong are worse than absent, because the next agent trusts them.
- **An empty section beats an invented one.** Leave the heading, leave it blank.
- **Never overwrite a doc wholesale.** Amend the lines that are wrong. Hand-written "why" is the most valuable content in any of these files and regeneration destroys it.
- **Stable contracts, not diary entries.** Docs describe how things are now. Delete superseded statements instead of narrating the change. `context/decisions/` is the one exception — that is where history is allowed to live.
- **Broad rules up, specific details down.** A rule that holds everywhere belongs in the rail. On conflict, the doc closest to the code wins on local detail; the rail wins on project-wide policy.
- **Keep it operational.** Prose an agent can act on. No filler, no restating the obvious, no summarizing the README.
- **Don't document generated or vendored trees.** `node_modules/`, `dist/`, `build/`, `target/`, `.venv/`, lockfiles.

## Report

Close with a coverage report that makes sparse output legible:

- **Route** — Bootstrap, Adopt, or Update, and why.
- **Reused** — existing README, PRD, specifications, schemas, or docs indexed instead of copied.
- **Localized** — subjects placed in nested `AGENTS.md` files and the boundaries that own them.
- **Created / amended** — every changed file and what changed.
- **Skipped** — likely context files not created because an existing source already covers them, no verified content exists, or the category does not apply.
- **Questions** — every `TODO(human)` marker and where it lives.
- **Untouched** — relevant files deliberately left alone and why.

Do not judge completion by the number of files under `context/`; account for every
relevant subject across the graph instead.

## References

Read the one you need, when you need it.

- `references/AGENTS-ROOT.md` — root rail format, protocol block, index format
- `references/AGENTS-CHILD.md` — nested `AGENTS.md` format and when a directory earns one
- `references/CONTEXT-FILES.md` — format for each `context/` file and for ADRs
- `references/UPDATE.md` — the drift check procedure
