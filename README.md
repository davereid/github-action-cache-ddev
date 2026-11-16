# Cache DDEV dependencies action

Caches the package downloads [DDEV](https://ddev.com) already keeps, so GitHub workflows don't download every Composer, npm and Yarn package on each run.

## Usage

Run it after DDEV is installed and before the project starts:

```yaml
on:
  pull_request:
  push:
    # Pull requests can only restore caches saved on their own branch or the default branch.
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: ddev/github-action-setup-ddev@v1
        with:
          autostart: false
      - uses: davereid/github-action-cache-ddev@v1
      - run: ddev start
      - run: ddev composer install
      - run: ddev npm ci
```

If DDEV is already running, the action restarts the project so it uses the caches.

## Options

### ddevDir

Path to your DDEV project, the directory that contains `.ddev/`.

default: `.`

### restart

Whether to restart the project when DDEV is already running. With `false`, the caches only apply from the next `ddev restart`.

default: `true`

## Outputs

### cache-hit

`true` when the cache was restored from an exact match for the lock files and DDEV version.

## How it works

DDEV keeps package caches in a Docker volume: `ddev-global-cache` up to v1.25, and a per-project volume after that. `actions/cache` can't reach a Docker volume, so the action writes `.ddev/docker-compose.cache-ddev.yaml`, which mounts directories on the runner over the cache directories in the `web` container. DDEV's own settings then write to the runner, and `actions/cache` saves those directories at the end of the job.

| Directory | Contents |
| --- | --- |
| `composer` | Composer cache |
| `corepack` | Package managers installed by Corepack |
| `n_prefix` | Node.js versions downloaded by `n` |
| `npm` | npm cache |
| `pip` | pip cache, if pip is installed |
| `yarn` | Yarn 1 and Yarn 4 caches |

Shell history and the Terminus cache, which holds a login session, aren't cached.

The cache key covers `composer.lock`, `package-lock.json`, `yarn.lock`, `.ddev/config.yaml` and the DDEV version. When any of them changes, the newest older cache is restored, and a new one is saved at the end of the job.

Caching DDEV's Docker images doesn't help: restoring them takes longer than pulling them ([ddev/github-action-setup-ddev#15](https://github.com/ddev/github-action-setup-ddev/issues/15)).
