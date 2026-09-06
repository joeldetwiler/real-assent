# Contributing

The working discipline for this repo: how work is identified, branched, committed,
reviewed, merged and released. It applies to everyone working in this repository,
including AI assistants.

## What belongs in this repo

Assume five contributors on three continents. Everything committed here has to be true
and useful for all of them.

- **Nothing machine-specific.** No absolute paths, no local directory layout, no
  configuration that only works on one box. Paths are relative to the repo root.
- **Nothing personal.** No individual named in the conventions, no private notes, no
  planning that lives somewhere only one person can reach.
- **Nothing about one person's process.** How an individual plans or tracks their own
  work is theirs. If a practice matters to everyone, write it here in full; if it does
  not, it does not belong here at all — including any description of where it lives.
- Local setup a contributor genuinely needs goes in an ignored file — `*.local.md`,
  `.env` — never in a tracked one, with a committed `.example` alongside it.

⚠️ **This applies to commit messages and PR descriptions too.** History is permanent, and
on a public repo it is permanently public.

## Branching model

**GitHub Flow, plus a tag on whatever gets deployed.**

- `main` is always deployable. Nothing else is permanent.
- Branch off `main`, do the work, open a pull request, squash-merge, delete the branch.
- Branches live hours to days. A branch still open after a week is a work item that was
  too big — split it.
- No `develop`, no `staging`, no `release/*`. Environments are infrastructure; branches
  are code. A deploy gate does not need a branch to live in.

Release branching stays available without being maintained: a tag is a branch point on
demand. If something already shipped needs patching, cut `fix/vX.Y.Z-<slug>` off the tag
at that moment, ship it, merge back, delete it.

### Branch names

    <type>/<issue>-<short-slug>

`<type>` is a Conventional Commit type (below); `<issue>` is the GitHub issue number.
Lowercase, hyphens, no trailing slashes.

    feat/12-selection-ui
    fix/31-contradiction-false-positive
    docs/2-git-discipline

Work with no issue skips the number: `chore/bump-vite`.

## Work items

**Every change of substance starts as a GitHub issue.** The issue holds the *what* and the
*why* and outlives the branch that closes it; the pull request holds the *how*. Between
them they are the record of why the code looks like this, which is the part nobody can
reconstruct later from the diff alone.

- **Open the issue before the branch.** One issue, one branch, one pull request. If it
  needs two branches, it was two issues.
- **Title it as the outcome, not the activity.** *"Selection screen persists answers"*,
  not *"work on selections"*.
- The issue number is the id everything else cites — branch name, PR body, commit footer.
- **Close issues through the pull request** (`Closes #12` in the PR body), never by hand.
  That is what links the issue to the commit that actually resolved it.

Trivial work — a typo, a dependency bump — needs no issue. If it needs explaining, it
needs an issue.

## Commits

[Conventional Commits](https://www.conventionalcommits.org/). Not enforced by tooling —
enforced by reading them.

    <type>(<scope>): <subject>

    <body — why, not what>

    Refs: #<issue>

| Type | For |
| --- | --- |
| `feat` | A capability the user can see |
| `fix` | A defect in shipped behavior |
| `refactor` | Behavior identical, structure better |
| `test` | Tests only |
| `docs` | Documentation, including this file |
| `content` | The authored commitment sets, glosses and position maps |
| `chore` | Dependencies, config, housekeeping |
| `ci` | Workflow and pipeline changes |
| `build` | Build tooling and bundler config |
| `perf` | Measurably faster, behavior unchanged |

Scopes, optional but preferred: `core`, `ui`, `data`, `content`, `repo`.

- Subject in the imperative, lowercase, no trailing period, 72 characters or fewer.
  *"add results screen"*, not *"added"* or *"adds"*.
- The body explains **why**. The diff already says what.
- `Refs: #<issue>` in the footer when there is an issue. Use `Refs:`, not `Closes:` —
  closing belongs to the pull request, so squashing cannot close an issue prematurely.
- Commits authored with Claude Code carry the `Co-Authored-By` and `Claude-Session`
  trailers. They go last.

## Pull requests

Every change reaches `main` through a PR. Yes, even working alone — especially then,
because Claude writes a large share of this code and **the PR is the surface where a
human actually reads the diff.** A change nobody read is not reviewed by virtue of being
small.

- **The PR title is a Conventional Commit subject.** Squash-merge uses it verbatim as the
  commit on `main`, so a sloppy title becomes permanent history.
- Open it early. Draft is fine and preferred over a branch nobody can see.
- The body says what changed and why, and carries `Closes #<issue>`.
- Read the full diff before merging. Every line, including the ones you are sure about.
- CI must be green, once CI exists.

## Merging

- **Squash and merge only.** Merge commits and rebase-merges are disabled in repo
  settings. `main` gets exactly one commit per PR and stays linear.
- Update a stale branch by rebasing it on `main`, not by merging `main` into it.
- Delete the branch on merge.
- **Never force-push `main`.** Force-pushing your own unmerged branch is fine.

## Releases

Semantic versioning, tagged on the `main` commit that gets deployed.

- Pre-1.0 (`v0.x.y`): a feature bumps the minor, a fix bumps the patch. Breaking changes
  are free — that is what `0.` means.
- The tag is the release record. No release branch is created or kept.

## Branch protection on `main`

- Require a pull request before merging.
- Require linear history.
- Block force-pushes and branch deletion.
- Require status checks once CI runs on pull requests.

Self-merge is allowed and expected. The gate is CI plus reading the diff, not a second
pair of eyes that does not exist.

> A branching model cannot be more advanced than the safety net under it. Until CI runs on
> pull requests, everything above is convention rather than enforcement — which makes
> standing up CI the highest-leverage item on the board, however unglamorous it looks.
