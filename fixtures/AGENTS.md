# Fixture lab

Shallow clones of public repositories used as disposable field-test subjects. Their
contents are ignored by the parent repository; this file is the tracked contract for
choosing, running, and reviewing fixture tests.

## Rules

1. Run skills against one fixture at a time and capture its initial `git status`, HEAD,
   and changed-file fingerprints. Without a baseline, a later run can misattribute an
   earlier fixture mutation.
2. Keep tests local. Do not commit or push generated fixture changes to the upstream
   remote; the result is the diff, transcript, and audit report.
3. Do not install dependencies unless the skill under test requires runtime evidence.
   Repository discovery and documentation tests should remain source-only, avoiding
   large generated trees and post-install hooks.
4. Preserve a useful failed run until it is audited. Stash it with
   `git stash push --include-untracked -m "<skill>-<case>"` before reusing that clone;
   the stash keeps evidence while returning tracked and untracked files to baseline.
5. Treat every child repository's own `AGENTS.md`, vendor instructions, and project
   documentation as fixture evidence. This parent rail governs lab handling, not the
   source project's local engineering rules.

## Fixture ladder

| Tier | Path | Source and pinned HEAD | Shape | Primary test |
| --- | --- | --- | --- | --- |
| Low | `low-sampleproject/` | [pypa/sampleproject](https://github.com/pypa/sampleproject) at `621e4974ca25ce531773def586ba3ed8e736b3fc` | 12 tracked files; Python `pyproject.toml`; no agent docs | Sparse bootstrap without padding or invented boundaries |
| Medium | `medium-ripgrep/` | [BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep) at `3fce3b5bb0236da2df6d99672afb8a719642eca7` | 237 tracked files; Rust workspace; contributor, guide, FAQ, release, CI, and AI-policy docs; no agent docs | Reuse and authority mapping across substantial human documentation |
| High | `high-uv/` | [astral-sh/uv](https://github.com/astral-sh/uv) at `a61e5c918cedf9ecc7e831bae2650657ae413445` | 1,706 tracked files; Rust/Python workspace; root `AGENTS.md` and `CLAUDE.md` | Adopt behavior, preservation, multi-language boundaries, and existing agent instructions |
| Extreme | `extreme-vscode/` | [microsoft/vscode](https://github.com/microsoft/vscode) at `eff5556afcf7a77abefa5d5b83dc26ac895ff8bf` | 18,190 tracked files; TypeScript monorepo; root plus nested `AGENTS.md` rails | Hierarchical precedence, scope control, generated/vendor exclusion, and performance under a large graph |

The pins describe the current local checkouts. Refresh a fixture intentionally with a
new shallow clone, then update its pin and observed shape here in the same change.

## Test sequence

1. Start with Low. Stop when the skill pads sparse output or crosses its write boundary.
2. Continue to Medium after Low passes. Audit whether existing docs were indexed instead
   of copied or rewritten.
3. Continue to High after Medium passes. Audit preservation of existing agent files and
   contradictions between instruction sources.
4. Run Extreme last. Audit nested-rail reachability and whether the skill stays scoped
   instead of attempting to document the whole tree indiscriminately.
5. Compare the final status, fingerprints, and generated claims with the baseline. A
   green build or a polished report does not replace this diff audit.

## Current checkout characteristics

The shallow checkouts occupy approximately 0.1 MB, 3.9 MB, 45.3 MB, and 824.8 MB from
Low through Extreme on this machine. The Extreme fixture uses a partial clone, but its
working tree is fully checked out; Git may contact the origin if a missing object is
requested later.
