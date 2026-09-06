# Real Assent — working notes for Claude Code

Read this before doing anything in this repo. Git process lives in `CONTRIBUTING.md` and
is not repeated here; when the two disagree, `CONTRIBUTING.md` wins on process.

## What this is

A tool for finding out whether what you believe hangs together. Beliefs go in one at a
time as atomic, truth-apt claims. The app derives which named philosophical positions
those claims are consistent with, and flags when a new one contradicts an older one.

The name is Newman's distinction between *notional* assent — an abstract nod — and *real*
assent, a belief actually held and acted on.

⚠️ **The design target is someone with no philosophical background.** Not an academic, not
a hobbyist who already keeps notes. That decides more than it looks like it does:

- **No term of art inside a claim.** If a claim cannot be phrased in ordinary words it
  does not belong in the content set.
- **The label comes last.** Naming a position up front invites the user to adopt the whole
  package unexamined. Show the plain-language gloss, then the term, as a payoff.
- **Derivation is not entailment.** Several positions usually survive the same set of
  claims. Say *may be*, list the survivors, and name the question that separates them.
- **The output is a skill, not a profile.** Telling someone their beliefs conflict is a
  one-off result; teaching them to notice the next conflict unaided is the product.

## Current phase

**v1 is a purely client-side React app.** No server, no database, no authentication.

- The authored claim set is embedded in the app as data.
- Services inside the app stand in for API calls.
- Session state and test profiles live in `localStorage`, keyed by profile id.

React + .NET 10 + PostgreSQL remains the target architecture. Nothing in v1 may assume it
is absent forever.

## Architecture rules

These are expensive to retrofit. Do not cross one without saying so explicitly.

| Rule | Why |
| --- | --- |
| **`src/core/` imports nothing from React.** Domain types, derivation, contradiction logic and the API client all live there as plain TypeScript | It is the whole cost of a React Native port later, and it is what makes the stand-in services swappable now |
| **Components talk to `src/core` interfaces only** — never to storage or to a data module directly | The seam dies the first time a component imports the JSON |
| **The stand-in services are async** and return promises from day one | A synchronous read is the one thing that cannot become an HTTP call without touching every caller |
| **Embedded data is shaped like a wire response**, not like whatever renders conveniently | Otherwise the real API has to match a shape nobody designed |
| **Selections are stored dated and append-only**, never as a mutable map | How a position moved over time is part of the point, and it cannot be reconstructed later |
| **A profile is an id and nothing else** | The profile picker is a test harness. If profile leaks into the domain model, real auth touches everything |

No monorepo, and no `.code-workspace` — one repo, with shared editor settings in
`.vscode/`.

## Working in this repo

- **An issue exists before a branch does.** See `CONTRIBUTING.md`.
- **Never commit to `main`.** Branch protection enforces it; do not try to work around it.
- **Ask before acting outside the repo.** Pushing, opening or merging a pull request,
  changing repository settings, deploying — each is a separate decision, and permission
  for one is not permission for the next.
- **Nothing machine-specific or personal goes in a tracked file**, including commit
  messages. `CLAUDE.local.md` is gitignored if a session needs local-only notes.
