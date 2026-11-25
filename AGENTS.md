# AGENTS.md

A composite GitHub Action (`action.yml`) that caches DDEV's package downloads. `README.md` explains how it works.

## Conventions

- Commit messages and PR titles follow [Conventional Commits](https://www.conventionalcommits.org/); the PR title check enforces it, and release-please builds releases and the changelog from them.
- Pin every action to a full commit SHA with its version in a comment (`uses: owner/action@<sha> # vX.Y.Z`). Renovate keeps them up to date.
- Give each workflow `permissions: {}` and each job only the permissions it needs, with a comment saying why. The CI workflow runs actionlint and zizmor next to the tests.
- Keep the action working on the DDEV versions `.github/workflows/ci.yml` tests: the latest release and HEAD. Cache directories differ between DDEV versions (`/mnt/ddev-global-cache` up to v1.25, `/mnt/ddev-project-cache` after), so changes must handle both.
- Test fixtures live in `tests/fixtures/`, one DDEV project per package manager setup, named for it (`ddevProjComposerNpm`, `ddevProjYarn4`).
