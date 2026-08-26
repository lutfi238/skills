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
  workflows.md                 declared build / test / deploy commands
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

## Step 0 — Freeze the write boundary. Before anything else.

Run this checkpoint before the first edit:

1. Capture every changed and untracked path. With Git, use
   `git status --porcelain=v1 --untracked-files=all`.
2. Fingerprint the content or absence state of every pre-existing changed path. With
   Git, use `git hash-object --no-filters -- <path>` for files that exist. A status or
   diff-stat match is not proof that content stayed unchanged.
3. Declare the complete write set as repo-relative paths with `/` separators. Accept a
   target only when it matches `(^|/)AGENTS\.md$` or `^context/`. Existing README,
   source, config, ignore, docs, and vendor instruction files are protected evidence.
4. Send the user a checkpoint naming the baseline, writable targets, and protected
   dirty paths. Do not issue an edit, write, or patch call before this checkpoint.

Before every write tool call, compare its literal target with both the declared write
set and the allowlist. A failed comparison cancels the call; report the useful change as
protected-doc drift instead. Discovery never expands the write set silently: declare an
additional allowed target to the user before writing it.

At closeout, repeat the status and fingerprints. Every pre-existing path must either be
byte-identical or be an allowed declared target changed by this run. Every new or
changed path attributable to the run must be allowed and declared. An attributable
violation blocks completion; undo only this run's isolated edit when that cannot erase
pre-existing work, otherwise stop and report it.

## Step 1 — Survey. Always.

Never write a doc from a skim. Establish, from the repo itself:

- **Existing docs.** `AGENTS.md`, `CLAUDE.md`, `README.md`, `CONTEXT.md`, `.cursorrules`, `.github/copilot-instructions.md`, `docs/`, any nested `AGENTS.md`, and authoritative product or requirements docs such as `PRD.md`, `REQUIREMENTS.md`, and `SPEC.md`.
- **Whether each proposed doc is gitignored.** Check every intended `AGENTS.md` and `context/` path against all applicable ignore files, including nested ones; use `git check-ignore -v --no-index -- <path>` when Git is available. A repo that ignores agent docs has decided they stay local. Honour that: leave ignore files alone, identify each local-only node in the report, and warn when a tracked index points to an ignored node that will be absent from a fresh clone.
- **Project type and manifest.** `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `composer.json`, etc. Pull the real script names from it.
- **Real boundaries.** Which top-level directories are actual modules with their own purpose, versus incidental folders.
- **Declared commands versus executed commands.** Read commands from manifests or CI and label them as declared. Call a command verified only when you actually ran it successfully; do not turn source inspection into execution history.
- **Enough code to be correct.** Read the entry points and the largest modules. Depth beats coverage.

Build an authority map before creating files: subject → existing source → whether its
claims agree with current code/config → uncovered facts. Do not index a document as an
authority when a relevant claim conflicts with the repository; report the conflict and
index a reliable source instead, or leave that subject without an authority.

Verify every path before repeating it, including paths copied out of a manifest, lock file,
CI config, or ignore list. A configuration naming a path proves the configuration names it,
not that the path exists; stale entries are common, and repeating one manufactures a false
claim in a doc that is otherwise correct. When the entry is stale, report it as
protected-doc drift and state the real path instead of the named one.

When an architecture or ownership entry groups multiple paths, verify that its stated
responsibility is true for every path in the group. Split mixed groups rather than
assigning one path's purpose to its neighbours; directory names and partial overlap do
not prove shared responsibility.

Before the first write, send the user a proof ledger for newly authored factual claims
that are exceptional:

- **Absolute** — quote each use of `all`, `every`, `never`, `only`, `full`,
  `deliberate`, `broken`, or `does not sync`, and name the complete surface checked.
- **Human intent, purpose, scope, recommendation, or history** — quote claims about
  why the project exists, its intended audience or use, workflows authors endorse or
  limit, and historical rationale. Cite the user or human-authored source; observed
  implementation proves behaviour, not these claims.
- **Naming prohibition** — list each proposed glossary `_Avoid_` term and cite the user
  or human-authored source that rejects it. Code usage or the absence of an alias is not
  authority for a naming ban.

Inspect prose introduced by this pass. Exclude mandatory verbatim protocol or template
text supplied by this skill, literal commands, flags, paths, identifiers, and
byte-preserved human prose; those retain their existing authority. If the pass changes
the meaning of preserved prose, ledger the newly introduced meaning. Narrow or omit any
included claim without the required proof. An empty ledger is valid.

Then pick a route:

| Repo state | Route |
| --- | --- |
| A usable rail and indexed context graph already exist | **Update** |
| No usable rail, but dedicated human-authored guidance exists: agent instructions, `docs/`, contributing guides, policies, specifications, or standalone guides/FAQs | **Adopt** |
| No usable rail, and existing documentation is limited to ordinary entry points such as a README, manifests, CI configuration, or none | **Bootstrap** |

Evaluate the rows from top to bottom. A README alone does not select Adopt. State which
route you took and why before writing anything.

A rail is **usable** only when it carries the protocol block and an index whose entries
resolve. A file named `AGENTS.md` that holds rules but indexes nothing — including one that
only defers to another document — is guidance, not a rail: route to Adopt, preserve its text
byte-for-byte, and add the protocol and index around it. Without this test a repository with
a thin rail and unindexed nested rails matches no row, and the route becomes a coin flip.

## Bootstrap

1. **Write the rail first** — `AGENTS.md` at the root, per `references/AGENTS-ROOT.md`. It carries the protocol block, project-wide rules, and the index. Nothing else works without it.
2. **Reuse before creating.** Index authoritative existing docs for the subjects they already cover. Add only the `context/` files you can fill with verified, nonduplicated facts. Start with `architecture.md`, `workflows.md`, and `gotchas.md` when those subjects lack a home. Add `glossary.md` when the domain has real vocabulary and `conventions.md` when the codebase has consistent unwritten style. Add product context only from an authoritative document or the user, never by inferring intent from implementation. Skip a file rather than pad it: if skipping a file would only move its information to another file, it should not exist. Formats in `references/CONTEXT-FILES.md`.
3. **Create child `AGENTS.md` only at lasting boundaries** — a directory with its own purpose, its own rules, or its own contract with the rest of the system. Format in `references/AGENTS-CHILD.md`. Three well-placed child docs beat fifteen that restate the rail.
4. **Index everything, then account for what is absent.** Every `context/` file and every child `AGENTS.md` gets a line in the rail's index with a short "read this when…" hook. Unindexed means invisible. Close the index with a coverage note naming the nodes a reader would expect and not find, and why — sparse output is only legible if the absences are written down. Format in `references/AGENTS-ROOT.md`.
5. **Never create agent-specific instruction files.** Not `CLAUDE.md`, not `.cursorrules`, not `.github/copilot-instructions.md`. `AGENTS.md` is the deliverable. Some clients need a bridge file to see it — say so in your report and give the user the exact line, but let them decide. See `references/AGENTS-ROOT.md`.

No ADRs on bootstrap. You don't know why past decisions were made — say so with a `TODO(human)` marker instead of guessing.

## Adopt

Existing docs are evidence, and someone wrote them by hand. Preserve the human's words.

1. **Read what's there in full.** Treat it as the current contract.
2. **Map before moving.** Existing non-agent docs stay byte-identical: index reliable ones and report their drift. For facts already in `AGENTS.md`, prefer leaving true prose in place. Move a placement violation only when it materially harms routing; move the human's sentences verbatim before making a separate surgical correction.
3. **Verify while you map.** Run the drift checks from `references/UPDATE.md` on every claim. Correct a mechanically false claim only inside the allowed write set. Report conflicts in protected existing docs and do not promote them as authorities.
4. **Add the protocol block and the index** to the existing `AGENTS.md`. Keep their headings, their tone, their ordering. You are adding structure around their content, not replacing it.
5. **Fill gaps only.** If they already documented the architecture well, do not rewrite it into your own template.

Never delete a human-written statement because it doesn't fit the template. If it is
unverifiable, say so and leave it. If it is mechanically false in an allowed agent doc,
amend only the false lines and report the correction.

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

- **The Step 0 write firewall is blocking.** Protected paths are read-only evidence even
  when correcting them would be useful. Skipping its pre-write checkpoint or exact
  closeout comparison means the pass is incomplete.
- **Never delete or rewrite an existing agent file on your own initiative.** `CLAUDE.md`, `.cursorrules`, `GEMINI.md`, `.windsurfrules`, a hand-written `AGENTS.md` — read them, fold their content into the rail, and leave the originals exactly as they are. The user put them there. If the user asks you to remove one, do it — this rule constrains what you do unasked.
- **Never create an agent-specific instruction file.** `AGENTS.md` and `context/` are the deliverable. Report the bridge line for other clients; let the user add it.
- **Write only what you verified from a repository source of truth or the user.** Everything else is a `> TODO(human): <specific question>` marker. Implementation proves current behavior, not product intent or historical reasoning. Docs that are confidently wrong are worse than absent, because the next agent trusts them.
- **The proof ledger gates newly authored factual claims.** Apply its exemptions
  exactly; outside them, missing authority means narrow or omit the claim, never infer
  intent, history, or a naming ban from implementation.
- **An empty section beats an invented one.** Leave the heading, leave it blank.
- **Never overwrite a doc wholesale.** Amend the lines that are wrong. Hand-written "why" is the most valuable content in any of these files and regeneration destroys it.
- **Stable contracts, not diary entries.** Docs describe how things are now. Delete superseded statements instead of narrating the change. `context/decisions/` is the one exception — that is where history is allowed to live.
- **A session-state file is not a contract doc.** An existing `HANDOFF.md`, `PROGRESS.md`, or `STATUS.md` records where work stopped, not how the project is. Never create one, never delete or restructure one, never index it as the authority for a stable subject, and keep it out of `context/`. It sits outside the write allowlist, so report its drift instead of correcting it. See `references/CONTEXT-FILES.md`.
- **Broad rules up, specific details down.** A rule that holds everywhere belongs in the rail. On conflict, the doc closest to the code wins on local detail; the rail wins on project-wide policy.
- **Keep it operational, for two readers.** Prose an agent can act on and a person can scan. The generated docs are read by both: the rail's opener and every index hook must orient a human returning after a month, and below them stay terse. No filler, no restating the obvious, no summarizing the README.
- **Don't document generated or vendored trees.** `node_modules/`, `dist/`, `build/`, `target/`, `.venv/`, lockfiles.

## Report

Close with a coverage report that makes sparse output legible:

- **Route** — Bootstrap, Adopt, or Update, and why.
- **Baseline / write set** — pre-existing changed paths, declared targets, and confirmation that every attributable edit stayed inside the allowlist.
- **Reused** — existing README, PRD, specifications, schemas, or docs indexed instead of copied.
- **Localized** — subjects placed in nested `AGENTS.md` files and the boundaries that own them.
- **Created / amended** — every changed file and what changed.
- **Distribution** — ignored or otherwise local-only graph nodes and the indexes that depend on them.
- **Conflicts** — stale or contradictory protected docs left unchanged and whether they were excluded as authorities.
- **Validation** — checks actually executed and their outcomes, kept separate from commands merely declared by manifests or CI.
- **Skipped** — likely context files not created because an existing source already covers them, no verified content exists, or the category does not apply. The same absences belong in the rail's coverage note: this report is scrollback, that copy survives.
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
