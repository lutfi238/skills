# The drift pass

Docs rot silently. Nothing fails when a doc goes stale — the agent just quietly acts on a false claim. This pass finds the false claims.

The principle: **a doc is a set of assertions about the repo, and most of them are mechanically checkable.** Check them. Do not re-read the codebase and rewrite the docs from scratch — that destroys hand-written reasoning and reintroduces guesswork.

## Scope

**Scoped pass** — after a code change. Walk from the rail to each path you touched, and check only the docs on that chain plus any `context/` file mentioning what you changed. This is the common case.

**Full pass** — when asked to audit, or when the user says the docs are wrong. Every check below, every doc.

## Checks

### 1. Path claims

Extract every repo path mentioned in every doc — code fences, links, inline backticks. Confirm each exists.

- **Gone** → find where it moved. Moved: update the reference. Deleted: delete the claim, and delete the whole doc if that path was its subject.
- **Renamed** → update every mention, not just the first one you found.

Highest-yield check. Path references break constantly and break loudly once an agent tries to follow one.

### 2. Command claims

Every command in `workflows.md`, in any `Verify` section, and in the rail. Check each against the real source — `package.json` scripts, `Makefile` targets, `pyproject.toml`, CI workflow files.

- Script no longer exists → find its replacement, or delete the row.
- Script exists but does something different now → rewrite the description.
- CI runs a command the docs don't mention → add it.

Do not run destructive or long commands to verify them. Reading the manifest is the check.

### 3. Index integrity — both directions

- Every entry in the rail's index resolves to a file that exists.
- Every `context/*.md` on disk appears in the rail's index and in `context/README.md`.
- Every nested `AGENTS.md` on disk is reachable from the rail through a chain of indexes. Find all of them and trace each one up.

A doc nobody links to gets stale fastest, because nobody reads it to notice.

### 4. Orphan docs

Any `AGENTS.md` whose directory no longer exists, or now holds something unrelated. Delete it. Any `context/` file whose entire subject has been removed — delete it and its index entries.

### 5. Missing docs

Directories that became boundaries since the last pass. Apply the three tests in `AGENTS-CHILD.md`: lasting boundary, rules of its own, an agent could get it wrong. Only then create a doc.

Also look for new footguns for `gotchas.md`. Signals: a `// don't` or `// HACK` comment, a defensive wrapper added after an incident, a workaround with a linked issue, a test named after a bug.

### 6. Contradictions

Where two docs state the same fact, keep the more specific one and delete the other. Where two docs state *conflicting* facts, determine which is true from the code, fix that one, delete the other, and report the conflict — it usually means a rule was changed in one place and not the other.

### 7. Placement violations

Apply the rule in both directions:

- A `context/` fact that only applies to one directory → move it into that directory's doc.
- The same fact in three sibling docs → move it up to the parent, delete all three copies.
- A directory-specific rule sitting in the rail's project-wide list → push it down. Test: name a directory where the rule does not apply. If you can, it does not belong in the rail.

### 8. Glossary terms

Each term should still appear in the code. A term nothing uses anymore is either renamed — update it — or a concept that was removed — delete it.

### 9. Answered markers

Every `TODO(human)` marker: has the answer since become derivable from the code, or did the user answer it in conversation? Fill it in and remove the marker. Markers that sit unanswered for months should be either rewritten as a sharper question or dropped.

## Amend, never regenerate

- Change the wrong lines. Leave everything else byte-identical.
- Reformatting a file you did not otherwise change is noise. It hides real changes in the diff and it wastes the reader's attention.
- Preserve human phrasing. A slightly awkward sentence written by the person who debugged the problem is worth more than your polished rewrite.
- Never delete a human-written claim just because you cannot verify it. Unverifiable is not the same as false. Flag it in your report and leave it.

## Report

State plainly:

- **Corrected** — each claim that had drifted, and what it says now
- **Deleted** — what you removed, and why it was no longer true
- **Added** — new docs or sections, and what prompted each
- **Conflicts** — contradictions found, how you resolved them
- **Unverifiable** — claims you could not check, left in place
- **Skipped** — docs you deliberately did not touch, and why
- **Clean** — say so if nothing had drifted. That is a real result, and it is the one that tells the user the system is working.

Never report a pass as complete if you only checked some of it. Say which checks you ran.
