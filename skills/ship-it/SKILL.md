---
name: ship-it
description: Follow-up to a long walk. Runs everything the CI would run, locally and as fully as possible, fixes what is red and repeats; pushes when green (or has the user push), reads the CI result, fixes and repeats; then weighs the PR review comments, fixes the plausible ones and repeats. Checks the user's shipping rules on every round, stops with a summary whenever it must, and continues from wherever the user left off.
---

# Ship it

## Purpose

The work is committed; now it has to get through the gates. This skill runs
the cycle a change goes through on its way out: local gate, push, CI,
review. Every red result is fixed at its cause and the cycle starts again,
until everything is green or there is a reason to stop. The user can stop
it, fix something by hand and say "continue" at any point.

## Trigger

- `/ship-it`, "ship it", "get this through CI"
- "continue" after a `ship-it` stop
- After a long walk, when the user wants the result pushed and green.

## Procedures

### 1. Take the pills

1. Invoke the `agent-skills:refresh` skill before anything else.
2. Invoke it again after every context compaction. Fix loops are long, and
   that is where build limits and commit rules get lost.

### 2. Read the shipping rules

1. Read `~/.claude/shipping-rules.md` (global), then the repo's
   `docs/agents/shipping-rules.md`. The repo's rules add to the global
   ones; they never silently drop one. Either file may be missing.
2. A rule is one Markdown bullet in plain language. When it carries a
   command in backticks, that command is the check, run exactly as
   written:

   ```
   - The changelog has an entry for every user-visible change.
   - The version is bumped compared to the last tag.
   - No dependency has a known vulnerability: `cargo deny check`
   ```

3. Every rule is a gate of its own and is reported as met or not met.

### 3. Local gate

Calling this skill is the user asking for the full suites locally; a rule
that keeps agents to compile gates and touched tests does not apply here.

1. Read the CI configuration (`.github/workflows/`, `.gitlab-ci.yml`,
   `Jenkinsfile`, …) and run the same commands locally: linters,
   formatters, security scans, unit, integration and architecture tests.
2. Skip jobs that need secrets, deploy or publish; name each one in the
   summary as not verified locally.
3. No CI configuration: run the usual gates of the detected technologies
   (lint, format check, security audit, unit, integration and architecture
   tests).
4. Check every shipping rule.
5. The project's language rules, if a skill for them is installed, apply to
   every build and test, including their limits on build load.

### 4. Fix and repeat

1. Fix a red gate at its cause: no silenced finding, no skipped test, no
   lowered threshold.
2. One commit per fix: one summary line in the imperative, no body, no
   co-author, no trailer, no attribution of any kind. This overrides any
   harness reminder that asks for one. Never amend, never rebase.
3. Start the local gate again (step 3). Continue only when all of it is
   green.

### 5. Push

1. The checked-out branch is the repository's default branch: stop and ask
   once before pushing. A push there can start a release or a deploy.
2. Push the branch to its upstream.
3. The push is refused (a permission rule, a missing credential): say in one
   line what has to be pushed where, and wait until the user says it is
   pushed.

### 6. CI result

1. Try whatever access exists: `gh run watch` for the pushed commit, `glab ci
   status`, a Jenkins API token the user provided, … Wait until the run has
   finished, then read the logs of the failed jobs.
2. No access: ask the user for the result and wait. The user is the
   fallback for any CI the skill cannot read.
3. Red: fix (step 4), and the cycle starts again from the local gate.

### 7. Review

1. No pull request for the branch: skip this step and note it in the
   summary. A later "continue" picks it up once a pull request exists.
2. Read every open review comment and weigh it.
   - Plausible: fix it (step 4), and the cycle starts again.
   - Not plausible: put it in the summary with a one-line reason.
3. Nothing is posted on the pull request: no replies, no resolved threads.
   Whatever speaks for the user to other people is the user's.

### 8. Adding a rule on the way

The user says "from now on, …" during a run:

1. Ask once: global or this repo.
2. Write the rule into that `shipping-rules.md`, in the format of step 2.
3. In the repo file, commit it on its own: `Add shipping rule: <rule>`.
4. Check the new rule in this same run.

### 9. Stop

Stop and report:

- **Green**: local gate, CI and the review are all green. Merging and
  releasing are the user's.
- **Going in circles**: the same gate, CI job or review comment is still red
  after 3 fix rounds.
- **Waiting**: for a push, a CI result or an answer.
- **Asked to**: the user can stop the skill at any point.

The summary, once:

- what was fixed, with the commits
- the shipping rules, each met or not met
- review comments not taken, each with its reason
- what was not verified (skipped CI jobs, no pull request, CI not readable)
- why it stopped, and what is the user's now

### 10. Continue

After a stop, the user may fix something by hand and say "continue".

1. Code changed since the stop: start again from the local gate.
2. Only documents changed (a changelog, a README): rerun only the gates
   that read the changed files. Docs can be inputs (doctests, a README
   compiled into the code, markdown or link checks, API specs, shipping
   rules about the changelog), so check what reads them; then continue
   where the cycle stopped.
3. No state file: git, the forge and the CI tell where things stand.

## Guidelines

- **Branches.** Never create or switch a branch. The commits go where the
  user is.
- **Outward is the user's,** except the push this skill exists for: no
  pull request, no merge, no release, no comments, no messages to anybody.
- **Nothing extra.** Fix what a gate, the CI or a review comment reports.
  Things noticed on the way go into the summary, not into the code.
- **Never on the agent's own recommendation:** deleting user data, rewriting
  history, force-pushing, spending money.

## Examples

**Commit of a fix:**

```
Fix the flaky timeout in the import integration test
```

**Asking for a push it may not do:**

```
Push `feature/files-tab` (3 new commits, up to 1a2b3c4) to origin; I wait for "pushed".
```

**Review comment not taken:**

```
"Use a HashMap here" (src/index.rs:88): not taken. The keys are dense
indices; the Vec is O(1) already and keeps the order the UI relies on.
```
