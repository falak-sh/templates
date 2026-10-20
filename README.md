# Falak templates

The one-click app catalog of [Falak](https://falak.sh): 46 apps (n8n, Plausible, Ghost, Dify, Odoo, …), each a
Docker Compose stack plus a small `template.yaml` describing its inputs and public services.

Every merge to `main` publishes a catalog release. Running Falak panels fetch it within the hour, so a new or fixed
template reaches every install without a Falak release. Panels keep the last good catalog, and the one baked into their
image, whenever a download fails.

```
<slug>/template.yaml   name, version, inputs, public services, min_falak, …
<slug>/compose.yaml    the stack (Compose, with ${INPUT} and ${{ falak.url(service) }} placeholders)
<slug>/icon.svg        optional (or `icon: <Cool Icons key>` in template.yaml)
test                   check + boot a template locally, the same way CI does
```

## Test a template

Needs Docker (Compose v2) and `jq`.

```sh
./test n8n                    # Falak's own checks (latest release), image tags/architectures, then boot it
./test n8n --falak v1.2.0     # against a specific Falak release
./test n8n --falak min        # against the template's min_falak
./test n8n --static           # Falak's checks only, no boot
./test n8n --keep             # leave the stack running to poke at it
```

The checks run inside `ghcr.io/falak-sh/falak-control-plane` (`php artisan templates:render`), so a template that
passes here is one a panel of that version accepts and renders the same way. The boot brings the rendered stack up
with `docker compose up --wait` (every service's healthcheck must pass) and requests each public service on its port.

Working on Falak itself? `FALAK_CHECKER="php /path/to/falak/control-plane/artisan" ./test n8n` uses your checkout.

## Use your own catalog

Fork this repository, keep the release workflow, and point your panels at your fork in `/opt/falak/custom.env`:

```sh
FALAK_TEMPLATES_URL=https://github.com/<you>/templates/releases/latest/download
```

See [CONTRIBUTING.md](CONTRIBUTING.md) to add a template here.

## License

Apache 2.0. The apps themselves are under their own licenses.
