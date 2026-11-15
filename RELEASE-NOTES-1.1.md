# Kolibri Studio App 1.1 (draft release notes)

Not released. The Lead sets the tag `1.1` (see #2).

- **Upstream:** [learningequality/studio](https://github.com/learningequality/studio) commit
  [`53a2e6505f`](https://github.com/learningequality/studio/commit/53a2e6505f) (2025-01-24,
  `unstable`/`hotfixes`), as copied into
  [koenswings/service-kolibri-studio](https://github.com/koenswings/service-kolibri-studio). This is not
  the `v2024.12.03` release tag, so the image tags carry `2025.01.24`.
- **Images:**
  - `koenswings/kolibri-studio:1.0-2025.01.24`: production build (webpack prod + collectstatic in the image, gunicorn + whitenoise), also runs the celery worker;
  - `koenswings/kolibri-postgres:1.0-2024.12.03`: Postgres 12 + pgvector (unchanged);
  - `redis:7.2.17`;
  - MinIO: see the silo PR.
- **Changes since 1.0-2024.12.03:**
  - no webpack devserver and no fixed host port 4000;
  - same-origin file links (`/content/`) and uploads (`/content-upload/`, proxied to MinIO with the signed Host);
  - a celery worker;
  - the Site domain comes from the Pi's name (`<hostname>.local:<port>`) or `STUDIO_SITE_DOMAIN`;
  - one admin (`STUDIO_ADMIN_EMAIL` / `STUDIO_ADMIN_PASSWORD`) and no sample data;
  - Redis 6.0 -> 7.2.
