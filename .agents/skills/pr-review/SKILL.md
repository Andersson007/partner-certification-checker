---
name: pr-review
description: Reviews pull requests and code changes in the partner-certification-checker repository against project standards. Use when asked to review a PR, patch, diff, or set of code changes. Do not use for GitHub Issues or general Q&A.
---

# Skill: pr-review

Review pull requests and code changes in the `partner-certification-checker` repository against the project standards in `AGENTS.md`.

## Inputs

- `target` (optional): PR number, branch name, commit hash, or file path. If omitted, review current working changes via `git diff HEAD`.

## Approach

1. **Gather the changeset.** PR number → read changed files and diffs; branch/commit → `git diff <base>..<ref>` or `git show <ref>`; file path → read it in full; no target → `git diff HEAD`. Read every changed file completely before judging.
2. **Work through the checks below in a single pass** over the changeset you already read in step 1, collecting findings per category. These are independent review lenses over the same diff — do not spawn subagents or background sessions for them; that re-reads or re-copies the same context without doing the work any faster.
3. **Report** using the Output Format.

## Review Checklist

The rules for most categories live in `AGENTS.md` — apply them from there rather than duplicating; the items below add PR-review-specific checks and pointers.

- **Workflow inputs/outputs** — new/changed `inputs:` on `certification-reusable.yml` have `description`, `type`, and a sensible `default` unless genuinely required; new outputs are documented; defaults preserve existing caller behavior unless an intentional, flagged breaking change.
- **Permissions & concurrency** — `permissions:` stay least-privilege (e.g. `contents: read`) with any added scope justified; entry-point workflows (`certification.yml`, `certification-reusable.yml`) keep a `concurrency:` group.
- **Security & template injection** — no secrets via `with:`/inputs outside `env:` without a documented `# zizmor: ignore[secrets-outside-env]`; no untrusted `${{ }}` (e.g. `github.event.*`) interpolated directly into `run:` blocks; would pass `nox -e zizmor`.
- **Supply-chain pinning** — per the pinning rules in `AGENTS.md` (Coding Guidelines) and `RELEASING.md`.
- **Backwards compatibility** — per the breaking-changes rules in `AGENTS.md` (Development Conventions) and `RELEASING.md`; also confirm the README ["Tested ansible-core branches and Python versions"](../../README.md#tested-ansible-core-branches-and-python-versions) table is updated when defaults/versions change.
- **Changelog fragment** — per the fragment rules in `AGENTS.md` (Development Conventions).
- **Sample/fixture collection** (only when `plugins/` or `tests/fixtures/collection/` is touched) — per the collection-content rules in `AGENTS.md`.
- **Lint & tests** — would pass the commands in `.agents/skills/run-tests/SKILL.md` relevant to the changeset.
- **Code quality** — no dead/commented-out/debug code; no shims or premature abstractions for hypothetical use; no injection or hardcoded credentials.

## Output Format

```
## PR Review: <target or "Current Changes">

### Summary
<One-paragraph overall assessment: scope, general quality, primary concerns.>

### Findings

#### Blockers (must fix before merge)
- [CATEGORY] <File>:<line> — <description>

#### Warnings (should fix, not strictly blocking)
- [CATEGORY] <File>:<line> — <description>

#### Suggestions (optional improvements)
- [CATEGORY] <File>:<line> — <description>

### Verdict
APPROVE / REQUEST CHANGES / COMMENT — <one sentence justifying it>
```

Be specific: always reference the file and line number when citing a finding. Omit categories that do not apply to the changeset.
