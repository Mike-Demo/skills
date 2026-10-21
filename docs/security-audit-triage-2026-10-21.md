# Security audit triage backlog

Source: [parent issue #1](https://github.com/Mike-Demo/skills/issues/1). This is a prioritized, ready-to-file backlog because issue creation was not available in this session. No findings have been fixed in this triage task.

## Priority

1. **Git guardrails hook bypass and fail-open.** Both confirmed findings are medium, but this hook is intended to stop destructive commands issued through Claude Code. Ordinary variants evade the patterns, making the protection unreliable against lower-trust agent instructions. The smallest fix needs careful command parsing and fail-closed handling.
2. **Existing skill directory deletion by the linker.** The script can delete local skill data, but only when a user runs the linker and has a conflicting non-symlink directory. Skipping and reporting the conflict is a small, safe fix.

These findings have distinct root causes and should be tracked separately.

## Remediation issue draft 1

**Title:** `[security][medium] Make the git guardrails hook robust and fail closed`

**Body:**

> Parent: [Security audit 2026-10-21](https://github.com/Mike-Demo/skills/issues/1)

**Fingerprint:** `git-guardrails-pattern-bypass-and-fail-open`

**Evidence:** `skills/misc/git-guardrails-claude-code/scripts/block-dangerous-git.sh` lines 3-4 extracts the command using `jq` without checking that `jq` succeeded or that a command was returned. Lines 6-16 define literal patterns; lines 18-25 only block matches and otherwise exit successfully. The hook advertises blocking destructive Git commands, but ordinary command spellings and variants can evade those literal substring checks.

**Root cause:** The hook checks literal substrings instead of interpreting the Git invocation. Separators, global Git flags, and combined short flags can evade the patterns. It also fails open if `jq` is unavailable, parsing fails, or the command is empty.

**Smallest fix:** Parse the command into argv, or use word-boundary matching that accounts for global Git flags such as `-C`, `--` separators, and combined short flags (including any `clean` flag bundle containing `f`). Fail closed with exit status 2 if `jq` is unavailable or `.tool_input.command` is empty or unparseable.

## Remediation issue draft 2

**Title:** `[security][medium] Preserve existing skill directories in link-skills.sh`

**Body:**

> Parent: [Security audit 2026-10-21](https://github.com/Mike-Demo/skills/issues/1)

**Fingerprint:** `link-skills-rm-rf-preexisting-skill-dirs`

**Evidence:** `scripts/link-skills.sh` lines 50-59 iterate over repository skill names, remove an existing non-symlink target with `rm -rf`, and then create a symlink in its place. The targets are under `~/.claude/skills` or `~/.agents/skills`.

**Root cause:** Every matching non-symlink target is unconditionally deleted without a prompt, listing, backup, or content check.

**Smallest fix:** Never run `rm -rf` on a non-symlink target. Skip it and report the conflict, or move it to a timestamped backup directory, leaving resolution to the user.

## Needs-validation issue draft

**Title:** `[security][needs-validation] Audit leads to validate`

**Body:**

> Parent: [Security audit 2026-10-21](https://github.com/Mike-Demo/skills/issues/1)

Validate each lead below. Do not treat a lead as a confirmed finding unless the missing fact is established.

- [ ] **Plugin distribution auto-updates unpinned skill instructions, including model-invoked skills that handle credentials** (`plugin-autoupdate-unpinned-skill-instructions`). **Missing fact:** Anthropic Claude Code marketplace auto-update semantics: whether a new upstream commit that changes a shipped `SKILL.md` is applied to an installed plugin automatically and without a version bump in `.claude-plugin/plugin.json` or any per-update user prompt/review. This is a provider fact outside this repository.
- [ ] **Release workflow with contents/pull-requests write token consumes mutable action tags and npm lifecycle scripts** (`release-workflow-mutable-action-refs-write-token`). **Missing fact as supplied:** Whether the mutable tags `actions/checkout@v4`, `actions/setup-node@v4` and `changesets/action@v1` can be repointed by their publishers (tag protection/immutability in those upstream repos), and whether any GitHub repository/organisation settings outside this repo (environment protection rules, `GITHUB_TOKEN` restrictions) constrain the release job's `cont…`. The parent issue body truncates the missing fact at `cont…`; restore the full wording before filing if it is available.
- [ ] **Triage skill instructs the agent to check out external PRs and run their tests/commands on the maintainer's machine** (`triage-executes-external-pr-code`). **Missing fact as supplied:** Whether the harness that runs `/triage` (e.g. Claude Code or Codex) requires explicit per-command maintainer approval, or provides an OS sandbox, before the agent can check out an external PR and run its tests/commands as instructed by `skills/engineering/triage/SKILL.md` line 74. This permission/sandbox behaviour is defined by the harness, not by any… The parent issue body truncates the missing fact at `any…`; restore the full wording before filing if it is available.

## Scope

The rejected candidate `wizard-write-env-newline-injection` is intentionally not re-raised. This document does not change code or tests.
