# app-kolibri-studio
Compose file for the kolibri-studio app

**Not production-ready** and not a Kit-managed release yet (`app.yaml`:
“Needs proper audit before first Kit-managed release”). The current
`compose.yaml` still uses unpinned or dev image tags (`:latest_dev`,
`minio/minio:…-arm64`, `redis:6.0.9` without a digest), bind-mounts `.` at
`/src`, and is missing a Docker healthcheck on most services. Persistent data
that is declared uses relative `./data` binds (`./data/minio`,
`./data/postgres`), which matches the App Disk convention
([idea#141](https://github.com/koenswings/idea/issues/141)); the `.` bind and
the unpinned tags do not. Do not ship this compose to a school kit as-is.

`x-app.version` on main is **1.1** (not tagged). The latest git tag and GitHub Release is **1.0-2024.12.03**. Until 1.1 is tagged, that 1.0 release is the last published version.

## Release check against the previous release (idea#403)

- **Before tagging a release, run `script/check-base-ref.sh` in a full clone with tags, with `AAD_CHECK_COMPOSE` set:** `AAD_CHECK_COMPOSE=<agent-app-dev>/script/check-compose.mjs script/check-base-ref.sh`.
- It compares `compose.yaml` with the previous release tag (agent-app-dev `check-compose --base-ref`). A stateful image (MariaDB, MySQL, Postgres, Mongo, Redis) that moves to a new series needs an App major bump.
- **Previous release tag:** the highest `<major>.<minor>[-<upstream>]` tag that is an ancestor of HEAD and is not HEAD's own tag.
- **It fails** in a shallow clone (`git fetch --unshallow --tags`; CI: `fetch-depth: 0`) and in a clone with no release tags at all (`git fetch --tags`).
- **First release:** if the repo has release tags but none of them is an ancestor, it is skipped with a note. If there are no release tags at all, a real first release must set `CHECK_BASE_REF_FIRST_RELEASE=1`.
- `node --test tests/release-base-ref.test.mjs` tests it. The compare runs only when `AAD_CHECK_COMPOSE` is set.
