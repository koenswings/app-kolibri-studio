# app-kolibri-studio
Compose file for the kolibri-studio app

## Release check against the previous release (idea#403)

- **Before tagging a release, run `script/check-base-ref.sh` in a full clone with tags, with `AAD_CHECK_COMPOSE` set:** `AAD_CHECK_COMPOSE=<agent-app-dev>/script/check-compose.mjs script/check-base-ref.sh`.
- It compares `compose.yaml` with the previous release tag (agent-app-dev `check-compose --base-ref`). A stateful image (MariaDB, MySQL, Postgres, Mongo, Redis) that moves to a new series needs an App major bump.
- **Previous release tag:** the highest `<major>.<minor>[-<upstream>]` tag that is an ancestor of HEAD and is not HEAD's own tag.
- **It fails** in a shallow clone (`git fetch --unshallow --tags`; CI: `fetch-depth: 0`) and in a clone with no release tags at all (`git fetch --tags`).
- **First release:** if the repo has release tags but none of them is an ancestor, it is skipped with a note. If there are no release tags at all, a real first release must set `CHECK_BASE_REF_FIRST_RELEASE=1`.
- `node --test tests/release-base-ref.test.mjs` tests it. The compare runs only when `AAD_CHECK_COMPOSE` is set.

## Images (release blockers, #2)

- **studio-app** `koenswings/kolibri-studio:<tag>` is a production build from
  [koenswings/service-kolibri-studio](https://github.com/koenswings/service-kolibri-studio) `Dockerfile`:
  - `yarn run build` + `collectstatic` run in the image build;
  - gunicorn + whitenoise serve on 8080, with `contentcuration.idea_settings` and `RUN_MODE=k8s`;
  - `docker/idea-entrypoint.py` migrates the database and creates the bucket and one admin.
  - There is no webpack devserver, so no `4000` port.
- **postgres** `koenswings/kolibri-postgres:<tag>` comes from
  [koenswings/service-kolibri-postgres](https://github.com/koenswings/service-kolibri-postgres)
  (`postgres:12` + pgvector).
- **redis** `redis:6.0.9` is the official image (linux/arm64/v8 in the manifest list).
- Both custom images are built on an ARM64 Pi, so `docker manifest inspect` can't check them before
  they are published. Check `docker image inspect --format '{{.Architecture}}' <image>` on the Pi
  that builds them, then publish and pin `repo:tag@sha256:…`.
