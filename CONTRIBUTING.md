# Adding or fixing a template

1. Create `<slug>/template.yaml` and `<slug>/compose.yaml`. The format is documented in
   [Falak's COMPOSE_TEMPLATES.md §2](https://github.com/falak-sh/falak/blob/main/docs/COMPOSE_TEMPLATES.md#2-template-format-lane-b);
   the existing templates are good references (`umami` is small, `dify` is large).
2. Run `./test <slug>` until it passes.
3. Open a pull request. CI runs the same `./test`.

## What a catalog template needs

`./test` enforces all of these; they are listed so nothing surprises you.

- **`slug`** equals the directory name.
- **Pinned images**: a version tag or digest, never `latest`, and published for **linux/amd64 and linux/arm64**.
- **Every service** has a `healthcheck` and `restart: unless-stopped`.
- **Data in named volumes** only (no host paths). A `stateful: true` template keeps its data in at least one.
- **`docs`** links the app's self-hosting docs, **`min_memory_mb`** is set, and the **description** is one line
  (≤ 120 characters).
- **Public services** `expose` their port; `health_check_path` when `/` is not a good health answer.
- **Secrets** are inputs with `generate:` (never hard-coded), and every `${VAR}` in compose.yaml is an input.
- **Icon**: a [Cool Icons](https://coolicon.falak.sh) key (`n8n`, `docker/svgl-dark`) or `./icon.svg` (no scripts,
  event handlers or links).
- **`min_falak`** when the template needs a Falak feature newer than the oldest release that fetches this catalog
  (v1.2.0); older panels skip it.

## Changing a template

Bump `version` whenever compose.yaml changes. Apps already deployed from a template are never changed by a catalog
update; only new deploys use the new version.
