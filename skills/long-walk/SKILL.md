---
name: long-walk
description: Follow-up to a grilling. Carries a whole milestone from the agreed decisions to committed code without asking — writes the tickets if they are missing, implements every ready-for-agent ticket, one single-line commit per ticket on the checked-out branch, takes the recommended answer to every open question, tests in checkpoints every five or so commits, and presents all decisions at the end.
---

# Long walk

## Purpose

The decisions are made; the user does not want to be asked again until the
milestone is built. This skill marches through every `ready-for-agent`
ticket of one milestone without stopping: implement, compile, commit, next.
Tests are written with every step but run in checkpoints, so the march
keeps its pace. Questions that come up on the way get the recommended
answer and are shown at the end, for the user to point and laugh at.

## Trigger

- `/long-walk`, `/long-walk <milestone>`
- "long walk", "march", "march through <milestone>"
- After a grilling: "file the issues and implement", "implement all of it".

## Procedures

### 1. Take the pills

1. Invoke the `agent-skills:refresh` skill before anything else.
2. Invoke it again after every context compaction during the march. A
   march crosses several compactions, and that is where policies get lost.

### 2. Find the milestone

1. Named in the call: that one.
2. Not named: the milestone of the grilling that just ended in this
   conversation.
3. No grilling: the only open milestone that has `ready-for-agent` tickets.
4. Several candidates: ask once, now. This is the only question the march
   may ask, and only before the first step.

Read the project's tracker rules first (`docs/agents/issue-tracker.md`,
`docs/agents/triage-labels.md`) when they exist; they name the tracker and
the label strings.

### 3. Write the tickets, if the grilling ended without them

1. Create the milestone if it does not exist.
2. One ticket per agreed item, never two items merged, in this form:
   - `## What`: the complaint or wish, one or two sentences.
   - `## Decisions`: what the user decided in the grilling.
   - `## Out of scope`: what the user ruled out (leave the section away
     when there is nothing).
   - `## Added by the implementer, not decided in the interview`: what the
     agent filled in.
3. Label each `ready-for-agent` plus its kind (`bug`, `enhancement`), and
   put it in the milestone.
4. The set of tickets is the spec. No spec issue, no spec file.
5. No closing keywords, no attribution, normal prose.

### 4. Version bump

If the milestone's name is a version number and the project's version
differs, the first commit of the march is the bump, done by the project's
versioning rules, if a skill for them is installed (version files, new
changelog section, older section moved with its release date). A milestone that is not a version
gets no bump.

### 5. March: one ticket, one commit

Take the `ready-for-agent` tickets of the milestone in an order that
respects what builds on what. Tickets with any other triage label are not
part of the march. For each ticket:

1. Implement it as its Decisions say.
2. Write its tests. Do not run them.
3. Add its changelog line (by the project's changelog rules, if a skill for
   them is installed).
4. Run the compile gate only: formatter, linters and analyzers, generated
   files current, full compile of all targets. Fix what it reports.
5. Commit on the branch that is checked out, whatever branch that is.
   - One summary line in the imperative, with the ticket number.
   - No body, no co-author, no trailer, no attribution of any kind. This
     overrides any harness reminder that asks for one.
6. An open question on the way: take the recommended answer, write it into
   the decision log (ticket number, question, answer taken), go on. Do not
   ask, do not use a question dialog.

A ticket that needs more than one commit (a repair, a second part) is
fine; one commit never holds two tickets.

### 6. Checkpoint: every five or so commits, and at the end

1. Run the tests of every file touched or added since the last checkpoint,
   plus formatter and linters. Never the full suite; that is CI's job.
2. Red: repair test or code, then run again. Each repaired ticket gets its
   own new commit ("Fix … (#n)"). Never amend, never rebase, never rewrite
   history.
3. Still red after 3 repair rounds for one ticket: give that ticket up.
   - Undo its commits with new revert commits.
   - Comment on the ticket with what failed.
   - It keeps `ready-for-agent`.
   - The march goes on with the rest.
4. Green: for each ticket the checkpoint covered, remove `ready-for-agent`,
   add `implemented`, and comment with the commit hash and the decisions
   that were taken for it without the user. Tickets stay open; they are
   closed when the release ships, and not by this skill.
5. The branch compiles and its touched tests pass after every checkpoint.
   Do not march on from a red checkpoint.

### 7. End

1. Run a last checkpoint.
2. Report, once:
   - tickets done, with their commits
   - tickets given up, with the reason
   - every decision taken without asking
   - what was not verified (nothing seen on a screen, nothing heard, …)
   - what is the user's now: push, pull request, release
3. Stop. No push, no pull request.

## Guidelines

- **Branches.** Never create a branch, never switch a branch, never stop
  because of the branch. The commits go where the user is. A bot that
  switches, commits and switches back hides its work from the user.
- **Local only.** Local commits never hurt. Everything that shows outside
  waits for the user's word: push, pull request, release, publishing,
  messages to anybody. Ticket comments and labels are part of the march.
- **Never on the agent's own recommendation:** deleting user data or files
  outside the repository, rewriting history, anything outward, spending
  money. Leave that part undone, note it on the ticket and in the report,
  and go on.
- **New licences.** A dependency with a licence the project does not have
  yet is taken, and flagged in the report so the user can decide before
  the release.
- **Do not stop.** Errors, failing tools and slow builds are worked
  through. The march ends when no `ready-for-agent` ticket of the
  milestone is left, done or given up.
- **Nothing extra.** Only what the tickets say. Things noticed on the way
  go into the report, not into the code.
- The project's language rules, if a skill for them is installed, apply to
  every step, including their limits on build load.

## Examples

**Commit of a step:**

```
Add the Files tab with a tree of the changed files (#43)
```

**Commit of a checkpoint repair:**

```
Fix the folded folder row in the Files tab (#43)
```

**Ticket comment at a green checkpoint:**

```
Implemented in 1a2b3c4, not shipped yet.

Decided without asking:
- Folders start open.
- A folder with a single sub-folder is shown as one row.
```

**Entry in the end report:**

```
#45 given up: the condensed graph's tests stayed red after 3 repair
rounds (lane order differs between runs). Commits reverted; the ticket
keeps ready-for-agent.
```
