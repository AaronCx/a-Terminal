# Releasing

This repo mirrors the App Store: `main` is always the source of the newest
**approved** release, and the [releases page](https://github.com/AaronCx/a-plus-terminal/releases)
lists exactly the versions Apple has approved.

## Branch model

- **`develop`** — integration branch. Feature branches merge here via PR; CI
  runs on every PR. TestFlight builds ship from here.
- **`main`** — approved releases only. Protected: PRs only, no force pushes.
  App code reaches `main` only after Apple approves the build that carries it.
  (Docs-only changes — README, this file — may land via PR at any time; they
  don't ship in builds.)

## TestFlight builds

`scripts/ship-testflight.sh <build>` runs from `develop`: version-bump PR →
CI → merge → signed archive → metadata gate → upload → poll to VALID.

## When Apple approves version X.Y.Z (build N)

1. Find the commit the approved build was archived from — the
   `chore: bump build number to N` merge on `develop`.
2. Branch `release/vX.Y.Z` at that commit, push, open a PR to `main`, merge
   when green.
3. Tag that commit: `git tag -a vX.Y.Z -m "X.Y.Z (build N)"` and push the tag.
4. Create the GitHub release: title `X.Y.Z (build N)`, notes summarizing the
   user-facing changes with PR references, marked as the latest release.
5. Merge `main` back into `develop` so `develop` stays a superset.

## Invariants

- `git diff vX.Y.Z main` right after a release merge shows at most docs-only
  drift.
- No tag or release exists for a build Apple has not approved.
- The `review-demo` release is not an app release: it hosts the demo video
  whose URL is cited in App Store review notes. Keep it.
