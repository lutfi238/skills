# Handoff

Current checkpoint for `C:\_General\project_gabut\my-skills`. Read `AGENTS.md` before
continuing. Durable behavior belongs in `skills/context/SKILL.md` and its references;
keep this file limited to current verification state and unfinished work.

## Repository state

The repository publishes the cross-client `context` skill as `lutfi238/skills`. It is
pure Markdown and has no runtime dependencies.

Current committed revision:

```text
ab22dce Add cross-cutting index to AGENTS.md and enhance HANDOFF.md with current verification state details
```

Current parent worktree:

```text
 M AGENTS.md
 M HANDOFF.md
 M skills/context/SKILL.md
 M skills/context/references/AGENTS-ROOT.md
 M skills/context/references/UPDATE.md
```

These five edits are uncommitted and form one change: generated docs are now written for two
readers, the rail closes with a coverage note that makes sparse output legible, and three
gaps found during the fixture ladder are closed (see Final hardening).

## Functional status

The `context` skill passed the four-tier fixture contract at the uncommitted working
revision (`SKILL.md` SHA-256 `198905B9...`). Four runs, executed in order, each with its own
Step 0 baseline and closeout comparison:

| Tier | Fixture | Route | Result |
| --- | --- | --- | --- |
| Low | `fixtures/low-sampleproject/` | Bootstrap | Passed. `AGENTS.md` plus two `context/` files; `context/` held at 2 entries so no `context/README.md`. Coverage note explained five absences. |
| Medium | `fixtures/medium-ripgrep/` | Adopt | Passed after one self-caught defect. Six files written; `AI_POLICY.md`'s ban on autonomous agent contribution lifted to rule 1. |
| High | `fixtures/high-uv/` | Adopt | Passed. All 1,815 characters of the existing hand-written rail preserved verbatim; `CLAUDE.md` byte-identical; `context/` held at 1 entry. |
| Extreme | `fixtures/extreme-vscode/` | Adopt | Passed. Write set was one file out of 18,190 tracked. All seven nested rails byte-identical and indexed; no `context/` created. |

Extreme took **Adopt**, not the Update recorded for the previous revision. Its root rail is
five lines that defer to `.github/copilot-instructions.md` and index nothing, so it is not a
usable rail under the definition now in `SKILL.md`. The route table previously matched no row
for that shape.

The result establishes tested behavior, not universal correctness for every repository. All
four runs were source-level documentation audits: dependencies were not installed, and no
build, test, or lint command was executed. Every command written into a fixture is labelled
**declared**.

## Final hardening

Three gaps were found by the ladder and closed mid-run, so later tiers tested the fix:

- **Coverage entries must be inline code, not links** (found at Low). A coverage note names
  absent nodes; writing one as a markdown link makes it a broken index entry and fails the
  both-directions check. `UPDATE.md` now excludes coverage lines from link resolution.
- **Paths copied out of a manifest must be verified before being repeated** (found at
  Medium). The run asserted `crates/fuzz` existed because `Cargo.toml` excludes it; the
  directory does not exist — the crate is at `fuzz/`. A configuration naming a path proves
  only that the configuration names it. The run corrected its own two files and reported the
  stale manifest entry rather than editing `Cargo.toml`.
- **"Usable rail" needed a definition** (found at Extreme). A rail carrying rules but no
  index is guidance, not a rail; it now routes to Adopt with its text preserved.

`UPDATE.md` also now states that a path in a nested `AGENTS.md` resolves relative to that
doc's directory, not the repository root — checking from the root produces both false alarms
and missed breaks.

## Validation evidence

Discovery passed with:

```powershell
npx skills add . --list
```

Expected result: `Found 1 skill`, named `context`.

Source and all installed copies match. Content is byte-identical; the digest below is the
LF form written to every location:

```text
198905B97DE2424EA4CF0FE9BCB7EE9C196C5BFD7943A43C575E9A17E8A08D4E  skills/context/SKILL.md
198905B97DE2424EA4CF0FE9BCB7EE9C196C5BFD7943A43C575E9A17E8A08D4E  C:\Users\mulfis\.agents\skills\context\SKILL.md
198905B97DE2424EA4CF0FE9BCB7EE9C196C5BFD7943A43C575E9A17E8A08D4E  C:\Users\mulfis\.claude\skills\context\SKILL.md
198905B97DE2424EA4CF0FE9BCB7EE9C196C5BFD7943A43C575E9A17E8A08D4E  C:\Users\mulfis\.mirasim\skills\context\SKILL.md
```

The `.claude` path is a symlink to the `.agents` copy, so those two cannot diverge.

Do not install with `npx skills add . --skill context`. On this machine that wrote a
project-local `.agents/`, `.claude/`, and `skills-lock.json` into the repository and rewrote
the global copies with stale content converted to CRLF. The repo pollution was removed and
the global copies were replaced from source by file copy.

Also verified:

- `git diff --check` passed for the skill patch.
- Frontmatter contains only `name` and `description`.
- Every local reference linked by `SKILL.md` exists.
- All fixture write-set and protected-path checks passed, for all four tiers.

Not re-verified at this revision, and carried over from the run at `9514e1a`: that global
installation succeeded for supported agents, and that Eve and PromptScript do not support
global skill installation. This pass did not exercise the installer's global path — it
synced the three global copies by file copy after the installer misbehaved. Treat the agent
coverage list as unconfirmed until someone runs a real global install again.

Also carried over and not re-tested: the optional `skill-creator/scripts/quick_validate.py`
could not start because the tooling environment lacks `PyYAML`. Do not add PyYAML to this
repository — the repository contract forbids runtime dependencies. CLI discovery and direct
structural checks passed at this revision.

## Fixture evidence

All four fixture worktrees are clean at their pinned revisions. The passing output of this
revision is `stash@{0}` in each:

```text
low-sampleproject    context-v2-low
medium-ripgrep       context-v2-medium
high-uv              context-v2-high
extreme-vscode       context-v2-extreme
```

Earlier runs remain in older stashes. Inspect from the fixture directory with
`git stash show --stat stash@{0}` or `git stash show -p stash@{0}`. Preserve the stash and the
clean baseline unless a new test requires restoring it.

Protected drift reported and correctly left unrepaired:

- `medium-ripgrep` — `Cargo.toml:23` excludes `/crates/fuzz`, which does not exist; the fuzz
  crate is at `fuzz/`.
- `medium-ripgrep` — `crates/README.md` documents 32 of 71 crates, with no entry for
  `crates/uv` itself.
- `extreme-vscode` — `src/vs/platform/agentHost/common/state/AGENTS.md:63` and `:79` point to
  `../../../protocol.md`. No `protocol.md` exists anywhere in the tree, so this is not a
  relative-depth error and the correct target is unknown.
- `extreme-vscode` — `extensions/copilot/src/extension/chatSessions/copilotcli/AGENTS.md:334`
  names `vscode-node/copilotCLIChatSessions.ts`; the file is at
  `../vscode-node/copilotCLIChatSessions.ts`.

The two `extreme-vscode` findings sit in nested `AGENTS.md` files, which the skill's own
allowlist would permit editing. They were left alone under `fixtures/AGENTS.md` rule 5, which
makes a child repository's own docs fixture evidence. That is a lab rule, not a skill rule —
in a real repository the skill would amend those lines and report the correction.

## Next action

The ladder passed on the working revision. Nothing is blocked.

1. Decide whether to commit the five working-tree files. They are one coherent change.
2. For any later change: run `npx skills add . --list` and `git diff --check`, sync the global
   copies from source by file copy, then re-run fixtures Low through Extreme in order,
   stopping at the first boundary or factual failure.
3. Stash useful results before returning each fixture to its clean pinned baseline.

Do not commit or push fixture changes.
