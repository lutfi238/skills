# Handoff

Current checkpoint for `C:\_General\project_gabut\my-skills`. Read `AGENTS.md` before
continuing. Durable behavior belongs in `skills/context/SKILL.md` and its references;
keep this file limited to current verification state and unfinished work.

## Repository state

The repository publishes the cross-client `context` skill as `lutfi238/skills`. It is
pure Markdown and has no runtime dependencies.

Current committed revision:

```text
9514e1a Refine proof ledger guidelines for exceptional claims and clarify documentation responsibilities
```

That revision matches `origin/main`. Its only change is
`skills/context/SKILL.md` (`24` insertions, `10` deletions).

Current parent worktree before this handoff update:

```text
 M .gitignore
?? HANDOFF.md
```

The `.gitignore` modification removes `HANDOFF.md` from the ignore list. Treat it as a
pre-existing user change; do not amend or revert it without an explicit request. This
handoff is consequently untracked until the user decides whether to commit it.

## Functional status

The `context` skill is functional against the current four-tier fixture contract. Four
fresh independent subagent runs passed on the same installed revision:

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

The final source and installed copies share this SHA-256:

```text
C0D319C5EBC66A169EED37283DF2A6705F0B8DC6FEADE2FF1AA0D56C0D378FE8
```

Matching locations:

```text
skills/context/SKILL.md
C:\Users\mulfis\.agents\skills\context\SKILL.md
C:\Users\mulfis\.claude\skills\context\SKILL.md
C:\Users\mulfis\.mirasim\skills\context\SKILL.md
```

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

No skill fix is currently required by the fixture ladder. For another change:

1. Preserve the current parent worktree baseline.
2. Run discovery and `git diff --check` after editing.
3. Install the candidate skill globally and confirm the installed hash matches source.
4. Run fixtures Low through Extreme in order, stopping at the first boundary or factual
   failure.
5. Stash useful results before returning each fixture to its clean pinned baseline.

Do not commit or push fixture changes. Do not commit the parent `.gitignore` or this
handoff until the user chooses how those two files should be handled.
