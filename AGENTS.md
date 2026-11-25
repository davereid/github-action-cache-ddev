# AGENTS.md

A composite GitHub Action (`action.yml`) that caches DDEV's package downloads. `README.md` explains how it works.

## Conventions

- Commit messages and PR titles follow [Conventional Commits](https://www.conventionalcommits.org/); the PR title check enforces it, and release-please builds releases and the changelog from them.
- Pin every action to a full commit SHA with its version in a comment (`uses: owner/action@<sha> # vX.Y.Z`). Renovate keeps them up to date.
- Give each workflow `permissions: {}` and each job only the permissions it needs, with a comment saying why. The CI workflow runs actionlint and zizmor next to the tests.
- Keep the action working on every DDEV version from the minimum in `action.yml` (v1.23.0). `.github/workflows/ci.yml` tests the last release of each minor from that minimum, the latest release and HEAD. When DDEV releases a new minor, add the previous minor's last release to the matrix, e.g. `1.25.x` once v1.26.0 is out: it's the last version with the `/mnt/ddev-global-cache` layout. To raise the minimum, change `action.yml`, the matrix, the "Fails on DDEV …" job and the README together. Cache directories differ between DDEV versions (`/mnt/ddev-global-cache` up to v1.25, `/mnt/ddev-project-cache` after), so changes must handle both.
- Test fixtures live in `tests/fixtures/`, one DDEV project per package manager setup, named for it (`ddevProjComposerNpm`, `ddevProjYarn4`).
