# Handoff

Current checkpoint for `C:\_General\project_gabut\my-skills`. Read `AGENTS.md` before
continuing. Durable behavior belongs in `skills/context/SKILL.md` and its references;
keep this file limited to current verification state and unfinished work.

## Repository state

The repository publishes the cross-client `context` skill as `lutfi238/skills`. It is
pure Markdown and has no runtime dependencies.

Current committed revision:

```text
7b4d19b Remove Handoff.md from .gitignore and add initial Handoff documentation
```

That commit resolved the two files the previous handoff left pending: `HANDOFF.md` is now
tracked, and `.gitignore` no longer ignores it. No decision on those two remains open.

Current parent worktree:

```text
 M AGENTS.md
 M HANDOFF.md
 M skills/context/SKILL.md
 M skills/context/references/CONTEXT-FILES.md
 M skills/context/references/UPDATE.md
```

These five edits are uncommitted and belong to one change: it adds the session-state
document genre, a file-creation test, the gotchas versus known-issues distinction, and a
cross-cutting index group to the repository's own rail.

## Functional status

The `context` skill passed the four-tier fixture contract at revision `9514e1a`
(`SKILL.md` SHA-256 `C0D319C5...`). Four fresh independent subagent runs passed on that
installed revision:

| Tier | Fixture | Route | Result |
| --- | --- | --- | --- |
| Low | `fixtures/low-sampleproject/` | Bootstrap | Passed with one sparse root rail; the README-derived purpose claim was ledgered |
| Medium | `fixtures/medium-ripgrep/` | Adopt | Passed with a root rail and precise architecture context; substantive human docs were reused |
| High | `fixtures/high-uv/` | Adopt | Passed with preserved human instructions and separate ownership entries for CI and agent assets |
| Extreme | `fixtures/extreme-vscode/` | Update | Passed with all seven nested rails indexed and protected drift reported without repair |

The result establishes the tested behavior, not universal correctness for every
repository. The fixture runs were source-level documentation audits: dependencies were
not installed, and build/runtime commands were not executed unless already available
without installation.

**The uncommitted working revision has not been run against the ladder.** The rules added
since `9514e1a` are untested: session-state handling, the file-creation test, the gotchas
versus known-issues boundary, and the cross-cutting index entries. Treat the table above as
evidence for `9514e1a` only.

## Final hardening

The final patch made four observed failure modes explicit:

- Evaluate route selection in `Update`, `Adopt`, then `Bootstrap` order. A README alone
  does not select Adopt.
- Apply the proof ledger to newly authored factual claims. Mandatory template text,
  literal commands/flags/paths/identifiers, and byte-preserved human prose retain their
  existing authority.
- Ledger human purpose, audience, intended use, workflow recommendations or limits,
  and history. Implementation proves behavior, not author intent.
- Split architecture entries when one responsibility is not true for every grouped
  path. Directory names or partial overlap do not prove shared ownership.

## Validation evidence

Discovery passed with:

```powershell
npx skills add . --list
```

Expected result: `Found 1 skill`, named `context`.

Source and installed copies currently differ. The working `SKILL.md` is ahead of every
installed copy, so no installed agent is running the working rules:

```text
100F59EF5B704FA848284AC2695AA803107971A72ECE662851A57398631F77C7  skills/context/SKILL.md
C0D319C5EBC66A169EED37283DF2A6705F0B8DC6FEADE2FF1AA0D56C0D378FE8  C:\Users\mulfis\.agents\skills\context\SKILL.md
C0D319C5EBC66A169EED37283DF2A6705F0B8DC6FEADE2FF1AA0D56C0D378FE8  C:\Users\mulfis\.claude\skills\context\SKILL.md
C0D319C5EBC66A169EED37283DF2A6705F0B8DC6FEADE2FF1AA0D56C0D378FE8  C:\Users\mulfis\.mirasim\skills\context\SKILL.md
```

Reinstall before any fixture run, then confirm all four hashes match again.

Also verified:

- `git diff --check` passed for the skill patch.
- Frontmatter contains only `name` and `description`.
- Every local reference linked by `SKILL.md` exists.
- All fixture write-set and protected-path checks passed.
- Global installation succeeded for supported agents. Eve and PromptScript reported
  that they do not support global skill installation.

The optional `skill-creator/scripts/quick_validate.py` could not start because the
tooling environment lacks `PyYAML`. Do not add PyYAML to this repository: the repository
contract forbids runtime dependencies. CLI discovery and direct structural checks
passed.

## Fixture evidence

All four fixture worktrees are clean at their pinned revisions. The final passing output
is `stash@{0}` inside each fixture:

```text
low-sampleproject    context-final-pass-low
medium-ripgrep       context-final-pass-medium
high-uv              context-final-pass-high
extreme-vscode       context-final-pass-extreme
```

Earlier failed or intermediate runs remain in older stashes. Inspect evidence from the
fixture directory with `git stash show --stat stash@{0}` or
`git stash show -p stash@{0}`. Preserve the stash and clean baseline unless a new test
requires restoring it.

The Extreme run reported these protected broken references and correctly left them
unchanged:

- `src/vs/platform/agentHost/common/state/AGENTS.md:63` and `:79` point to absent
  `../../../protocol.md`.
- `extensions/copilot/src/extension/chatSessions/copilotcli/AGENTS.md:334` points to
  `vscode-node/copilotCLIChatSessions.ts`; the observed relative target is
  `../vscode-node/copilotCLIChatSessions.ts`.

## Next action

Revalidate the uncommitted working revision. It has not been run against the ladder:

1. Reinstall the skill globally and confirm the installed hash matches source.
2. Run fixtures Low through Extreme in order, stopping at the first boundary or factual
   failure. Watch specifically for the untested rules: whether the file-creation test
   suppresses a file that would only relocate information, and whether a fixture carrying
   its own session-state file is left unedited and indexed narrowly.
3. Stash useful results before returning each fixture to its clean pinned baseline.
4. Commit only after the ladder passes on the working revision.

For any subsequent change, run discovery and `git diff --check` after editing, then repeat
the sequence above.

Do not commit or push fixture changes.
